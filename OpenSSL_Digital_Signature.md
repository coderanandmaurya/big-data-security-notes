## 🧾 Task Objective

You’ll:

1. Create a private and public key pair.
2. Use the private key to **digitally sign** a document (text or CSV).
3. Verify the signature using the **public key**.

---

## 🧰 Prerequisites

1. **Install OpenSSL** for Windows:

   * Download from: [https://slproweb.com/products/Win32OpenSSL.html](https://slproweb.com/products/Win32OpenSSL.html)
   * Add OpenSSL’s `bin` directory to your **System PATH** (so you can use `openssl` from cmd).

2. **Create a working folder**
   Example: `C:\openssl_demo`

---

## 📄 Step 1: Create a Synthetic Dataset (Document)

Let’s create a small text or CSV file.

### Example 1 (Text file)

```text
employee_data.txt
-----------------
EmployeeID, Name, Salary
101, Alice, 50000
102, Bob, 48000
103, Charlie, 52000
```

Save it as `employee_data.txt` inside `C:\openssl_demo`.

---

## 🔑 Step 2: Generate Private and Public Keys

Open **Command Prompt** and navigate:

```bash
cd C:\openssl_demo
```

### Generate a Private Key (RSA 2048-bit)

```bash
openssl genrsa -out private_key.pem 2048
```

### Extract the Public Key

```bash
openssl rsa -in private_key.pem -pubout -out public_key.pem
```

Now you have:

* `private_key.pem`
* `public_key.pem`

---

## ✍️ Step 3: Create a Digital Signature for the Document

You’ll use your **private key** to sign the document.

```bash
openssl dgst -sha256 -sign private_key.pem -out signature.bin employee_data.txt
```

* `dgst`: digest command (for message digest and signing)
* `-sha256`: hashing algorithm used
* `-sign`: uses the private key to sign
* `signature.bin`: output binary signature file

---

## 🔍 Step 4: Verify the Signature Using the Public Key

To verify:

```bash
openssl dgst -sha256 -verify public_key.pem -signature signature.bin employee_data.txt
```

✅ Expected output:

```
Verified OK
```

If you modify even one character in `employee_data.txt`, you’ll get:

```
Verification Failure
```

---

## 🧪 (Optional) Step 5: Convert Signature to Base64 for Readability

To view or send the signature as text:

```bash
openssl base64 -in signature.bin -out signature_base64.txt
```

To verify using the Base64 version:

```bash
openssl base64 -d -in signature_base64.txt -out signature.bin
openssl dgst -sha256 -verify public_key.pem -signature signature.bin employee_data.txt
```

---

## 📊 Example File Summary

| File                 | Description               |
| -------------------- | ------------------------- |
| employee_data.txt    | Original dataset          |
| private_key.pem      | Private RSA key           |
| public_key.pem       | Public RSA key            |
| signature.bin        | Digital signature file    |
| signature_base64.txt | Optional readable version |

---

## 💡 Concept Recap

* **Private Key:** Used to sign the data (proves authenticity).
* **Public Key:** Used to verify the signature (proves integrity).
* **Digital Signature:** A hash of the data encrypted with the private key.

---
