# 1. TCP internals — fields, flags, sequence control and reliability (deep)

## TCP header (important fields you should teach)

A TCP header is normally 20 bytes (without options). Key fields students must know:

* **Source port (16 bits)** — client port.
* **Destination port (16 bits)** — server port (e.g., 80).
* **Sequence number (32 bits)** — identifies byte offset of data sent; used to order/reassemble segments.
* **Acknowledgment number (32 bits)** — next expected sequence number (i.e., last received byte + 1).
* **Data offset (4 bits)** — TCP header length (where data begins).
* **Flags (9 bits/varies)** — common ones:

  * **SYN** — synchronize (start connection).
  * **ACK** — acknowledge.
  * **FIN** — finish (graceful close).
  * **RST** — reset (abrupt close / rejected).
  * **PSH** — push (deliver to app ASAP).
  * **URG** — urgent pointer valid.
  * **ECE/ CWR** — congestion control / ECN.
* **Window size (16 bits)** — flow control; how many bytes the receiver can accept.
* **Checksum (16 bits)** — integrity for header+data.
* **Urgent pointer, options (timestamp, MSS, SACK perms)** — options extend header (MSS, Window scaling, Timestamps, SACK).

## Sequence / ACK example (numeric)

* Client sends SYN with SEQ = 1000.
* Server sends SYN+ACK with SEQ = 3000, ACK = 1001 (meaning server acknowledges client’s SYN: 1000 + 1).
* Client sends ACK with SEQ = 1001, ACK = 3001.

Sequence numbers increment by bytes of payload; SYN/FIN consume one sequence number.

## Reliability mechanisms

* Retransmission: if no ACK within timeout, sender retransmits.
* Congestion control: slow-start, congestion avoidance, fast retransmit, fast recovery.
* Flow control: window advertisement prevents overwhelming receiver.
* Out-of-order delivery: sequence numbers let receiver reorder.

---

# 2. TCP handshake variants & anomalies (what students should observe)

* **Full three-way handshake** — SYN → SYN+ACK → ACK — produces ESTABLISHED state on both endpoints.
* **Half-open / SYN scan behavior** — attacker sends SYN; if server replies SYN+ACK, attacker sends RST (reset) instead of completing handshake. Server had briefly allocated resources but no full connection.
* **RST** indicates closed port (server explicitly refuses).
* **No response** (or ICMP unreachable) indicates *filtered* (firewall dropped or blocked).
* **SYN flood** — attacker sends many SYNs and never completes handshake, exhausting backlog; IDS/IPS detect by odd SST behavior.

---

# 3. Observing the handshake — packet capture (Wireshark / tcpdump)

Practical, safe exercise: have students observe what happens on the wire when they run an `nmap -sS` (SYN scan) and a normal `telnet` (full handshake + data) to the same port.

### Capture with tcpdump (class demo)

```bash
# on attacker machine (sudo required)
sudo tcpdump -i <interface> tcp and host 192.168.33.131 -w capture.pcap
# do your scans / connections, then stop tcpdump (Ctrl+C)
```

Open `capture.pcap` in Wireshark. Look for:

* Packets with `[S]` (SYN), `[S.]` (SYN+ACK), `[.]` (ACK), `[R]` (RST).
* Compare timestamps, SEQ/ACK values.

### What students should see

* For a normal connection, three packets (SYN, SYN+ACK, ACK), then application data packets.
* For SYN scan: SYN, SYN+ACK, then attacker sends RST — you'll see no application data; OS might log a short connection attempt.

---

# 4. UDP internals — why UDP scans are different

* UDP header is simple: src port, dst port, length, checksum. No handshake or ACKs.
* For scanning:

  * Open UDP port often returns a UDP response (protocol-specific).
  * Closed UDP port often returns ICMP Port Unreachable (type 3, code 3).
  * Lack of response → *open|filtered* — ambiguous.
* UDP scans must wait for timeouts and ICMP rate-limiting — hence much slower.

---

# 5. Nmap — exhaustive scan types and what they do (detailed list)

Students must know each scan type semantics and when to use them:

### Common TCP scan types

* `-sS` **SYN scan (half-open / stealth)**
  Sends SYN; if SYN+ACK → port open (then send RST); if RST → closed. Fast, stealthy.
* `-sT` **TCP connect scan**
  Uses OS connect() to complete full handshake. Works without raw socket privileges. No stealth; more likely logged.
* `-sU` **UDP scan**
  Sends UDP packets to probe UDP services. Slow.
* `-sV` **Version detection**
  Sends probes to identify application and version. Use with care (can be noisy).
* `-O` **OS detection**
  Fingerprints TCP/IP stacks; uses a variety of probes.
* `-A` **Aggressive**
  Equivalent to `-sV -O --script=default` plus traceroute — full recon.
* `-sA` **ACK scan**
  Used to map firewall rule behavior (determines if packet filtered or unfiltered); ACK probes do not tell open/closed.
* `-sN`, `-sF`, `-sX` **Null/FIN/Xmas scans**
  Specialized probes that rely on RFC TCP behavior — work on some Unix systems, less effective on modern stacks.
* `-sP` (deprecated) now `-sn` **Ping scan**
  Host discovery; no port probing.

### Timing templates

* `-T0` (paranoid) — very slow, stealthy.
* `-T1` (sneaky)
* `-T2` (polite)
* `-T3` (normal)
* `-T4` (aggressive) — good for LAN.
* `-T5` (insane) — fastest, noisy, may cause dropped probes.

### Port selection

* `-p` specify ports (e.g., `-p 21,22,80` or `-p-` for all 65535 ports).
* `--top-ports N` scan top N common ports.

### Output & saving

* `-oN file.txt` normal, `-oX file.xml` XML, `-oA base` all formats.

### Evasion/stealth flags (educational)

* `-f` **fragment packets** — splits probes into small fragments (may evade simplistic IPS). Use only in lab and discuss detection.
* `--data-length X` append arbitrary bytes to each probe.
* `-D DECAY` **decoy** — sends decoy traffic to obfuscate true source (teaches about attribution and how defenders detect false positives).
* `-S` **spoof source address** — requires careful network setup; can break things and causes attribution issues (teach only as theory).

> Instructor note: evasion flags are interesting to teach about detection & forensics — but do **not** let students use them on networks you don’t control.

---

# 6. Nmap Scripting Engine (NSE) — powerful and safe script examples

NSE scripts are grouped by categories: `auth`, `broadcast`, `brute`, `default`, `discovery`, `dos`, `exploit`, `external`, `fuzzer`, `intrusive`, `malware`, `safe`, `vuln`, etc.

**Safe scripts to show students** (benign/diagnostic):

* `--script=default` — basic checks & info (good starting point).
* `--script=banner` — grabs application banners (HTTP, SMTP, etc.).
* `--script=http-enum` — enumerates common web paths (safe if target is lab).
* `--script=smb-enum-shares` — enumerates SMB shares; useful to see share names (lab only).
* `--script=ftp-anon` — checks anonymous FTP login (non-destructive; lab only).

**Vulnerability-surfacing scripts (read-only, informative)**:

* `--script=vuln` runs multiple vulnerability-detection scripts. These may check for known problems, but avoid running intrusive exploit scripts in class unless controlled by instructor.

**How to run NSE safely**

```bash
sudo nmap --script=banner,http-enum -p 80,443 192.168.33.131
```

Interpret output: each script prints a section with findings. Use results to research CVE IDs, vendor advisories.

---

# 7. Example Nmap session (walkthrough with expected output)

Run (safe, reconnaissance-focused):

```bash
sudo nmap -sS -sV -O --script=banner --top-ports 200 -oN lab_scan.txt 192.168.33.131
```

**Example result (annotated)**:

```
PORT    STATE  SERVICE    VERSION
21/tcp  open   ftp        vsftpd 2.3.4
22/tcp  open   ssh        OpenSSH 4.7p1
23/tcp  open   telnet     Linux telnetd
80/tcp  open   http       Apache httpd 2.2.8 ((Ubuntu))
139/tcp open   netbios-ssn Samba smbd 3.0.20
445/tcp open   netbios-ssn Samba smbd 3.0.20
MAC Address: 08:00:27:3A:... (VirtualBox)
OS details: Linux 2.6.X
```

Explain each line: port number, state, named service, guessed version. Highlight how version strings map to known vulnerabilities (instructors should guide students to CVE search sites for the exact version and platform).

---

# 8. Netcat — extended safe uses & banner grabbing

Netcat is extremely useful *for benign diagnostics*:

### Banner grabbing (HTTP)

```bash
nc 192.168.33.131 80
GET / HTTP/1.1
Host: 192.168.33.131

# response: HTTP headers and page source
```

### Port scan (fast check)

```bash
nc -zv 192.168.33.131 1-1024
# Reports which ports responded
```

### File transfer (lab-only)

Receiver (listening):

```bash
nc -lvp 5555 > incoming.txt
```

Sender:

```bash
nc 192.168.33.10 5555 < outgoing.txt
```

### Warning about reverse shells & backdoors

A reverse shell is a legitimate administrative tool in some contexts but is also a common technique in unauthorized takeovers. I will **not** provide reverse shell recipes. Teach students about them conceptually (how they bypass firewalls), and emphasize detection/defense (Egress filtering, process monitoring, IDS signatures).

---

# 9. Metasploitable — safe classroom approach (how to structure exploit demonstrations)

Because “exploitation” can be misused, structure any exploit demonstration like this:

1. **Instructor-only demo** (in class) — instructor runs exploit from a controlled Kali box and captures full logs (pcap, Metasploit console output), then shares sanitized output with students. Do **not** let students run arbitrary exploits unsupervised.
2. **Guided lab** — instructor provides a prepared exploit script that is instrumented (limits effects, doesn’t write persistent backdoors) and runs only inside an isolated lab network. Students can inspect code, run static analysis, and discuss mitigation.
3. **Discussion** — after demo, walk through:

   * What was the vulnerable input (e.g., a malformed username, a buffer overflow)?
   * How did the exploit gain code execution? Shell? Upload files?
   * What controls would have prevented it? (patching, least privilege, network segmentation, IDS signatures)
4. **Clean-up** — revert VM snapshot.

---

# 10. Example lab syllabus — more detailed, with tasks & rubric

### Week 1: Reconnaissance & Discovery (2-hour lab)

* Tasks:

  * Find target IP using `nmap -sn`.
  * Run `sudo nmap -sS -sV -O --script=banner` and save output.
  * Capture traffic with `tcpdump` while scanning and annotate handshake traces in Wireshark.
* Deliverable: `lab1_results.txt`, `capture.pcap`, 500-word report interpreting findings.
* Grading (out of 100):

  * Correct scans & saved outputs — 30
  * Packet capture annotation — 30
  * Interpretation & CVE lookups — 40

### Week 2: Service enumeration & safe probing (2 hours)

* Tasks:

  * Use `nmap --script=ftp-anon,smb-enum-shares,http-enum` on the lab VM.
  * Use `nc` to fetch HTTP root and `curl -I` headers (demonstrate both).
* Deliverable: short write-up on services found and recommended mitigations.
* Rubric: accuracy, clarity, mitigation suggestions.

### Optional Week 3: Controlled exploit demo (instructor-led)

* Instructor runs selected exploit, students analyze logs and artifacts.
* Post-demo: students produce a threat report and remediation plan.

---

# 11. Defensive perspective — how a defender spots scans & attacks

Teach students two sides: offense (scanning) and defense (detecting).

**IDS/IPS signatures that detect scans**

* Many IDS rule sets detect port sweeps, SYN floods, repeated SYN with no ACK, decoy scans, and rapid port probes.
* Example patterns:

  * High rate of SYNs from single host to many ports → port sweep.
  * SYN without full handshake → stealth scan.
  * Unusual combinations of flags (Xmas, Null) flagged as suspicious.

**Hardening tips**

* Disable unused services.
* Patch frequently (close known CVEs).
* Use firewalls to restrict access (least privilege).
* Enable logging and centralize logs for correlation.
* Rate-limit ICMP and UDP responses.

---

# 12. Common errors & troubleshooting (extended)

* **No targets found**: verify networking mode (Host-only vs NAT vs Bridged). In VirtualBox, host-only gives direct VM-to-VM; NAT isolates.
* **Permission denied**: for `-sS` or `-O`, use `sudo`.
* **No response from UDP**: add `--reason` and `-v` to get more details; use `--script=udp-` safe scripts to see application behavior.
* **False results from OS detection**: NAT or virtualization can mask OS fingerprints; run multiple probes.

Useful nmap flags for debugging:

* `-v` / `-vv` — verbosity.
* `--reason` — show why a port was classified open/closed/filtered.
* `--packet-trace` — show low-level packet details that nmap sends/receives (great to teach how nmap determines states).

---

# 13. Example "expected outputs" & what to annotate (for reports)

Ask students to annotate these items in their lab report:

* Which ports were open and why those services matter to the host.
* Version strings and their release dates (map to CVE windows).
* Any weird banner or service misconfiguration (anonymous FTP, default creds).
* Evidence of IDS/Firewall filtering (e.g., `filtered` states).

---

# 14. Additional reading & teaching resources (to give to students)

* Nmap Book: *Nmap Network Scanning* (free PDF by Fyodor/Gordon Lyon).
* Wireshark: “Practical Packet Analysis” (look for TCP handshake chapter).
* OWASP Testing Guide — for web enumeration techniques.
* CVE Details / NVD — how to search CVE records by product/version.

---

