# Lecture: Secure Tokenization of Personally Identifiable Information (PII) Using Python's Cryptography Library

**Date:** Thursday, October 09, 2025  
**Time:** 09:16 AM IST  

---

## 🧠 Introduction

Welcome to this lecture on **Secure Tokenization of Personally Identifiable Information (PII) Using Python's Cryptography Library**.  
In today's data-driven world, handling sensitive information like names, Social Security Numbers (SSNs), email addresses, or financial details is commonplace. However, exposing such data can lead to **privacy breaches**, **identity theft**, or **regulatory violations** (e.g., under **GDPR**, **HIPAA**, or **CCPA**).

This lecture focuses on **tokenization**, a technique to protect sensitive data by replacing it with **non-sensitive equivalents (tokens)** that can be used in place of the original data without revealing it.

---

### 🎯 Learning Outcomes

By the end of this lecture, you will:

- Understand what **PII** is and why **tokenization** is essential.  
- Learn about **symmetric encryption** and **format-preserving encryption (FPE)**.  
- Walk through a complete **Python script** for PII tokenization.  
- Explore **best practices** for secure implementation and key management.

---

### 👩‍💻 Target Audience

This lecture is designed for **intermediate Python developers**, **data engineers**, and **security enthusiasts**.

**Prerequisites:**
- Basic knowledge of Python and pandas  
- Familiarity with cryptography concepts  

**Libraries Used:**
- `cryptography` (encryption)  
- `faker` (synthetic data generation)  
- `pandas` (data handling)  

---

## 🔍 What is PII and Tokenization?

### 🧩 Personally Identifiable Information (PII)

**PII** refers to any data that can be used to identify an individual, such as:

- **Names:** e.g., "John Doe"  
- **Social Security Numbers (SSNs):** 9-digit identifier (format: `XXX-XX-XXXX`)

**SSN Format Explanation:**
- First 3 digits: Area number (geographic region)
- Next 2 digits: Group number
- Last 4 digits: Serial number  

**Example:** `123-45-6789`

Other examples include email addresses, phone numbers, and physical addresses.

> ⚠️ **Note:** Handling PII requires caution because leaks can have severe consequences.  
In 2023, over **353 million records** were exposed in data breaches involving PII.

---

### 🔐 Tokenization

**Tokenization** replaces sensitive values with unique, non-sensitive tokens.

#### ✅ Benefits:
- **Security:** Hides sensitive data from unauthorized access.  
- **Usability:** Preserves format (e.g., SSN stays in `XXX-XX-XXXX`).  
- **Compliance:** Supports GDPR, HIPAA, and CCPA requirements.  
- **Reversibility:** Original data can be recovered with encryption keys.

#### 🧮 Tokenization Methods:
1. **Symmetric Encryption (Fernet):** For names — generates opaque tokens.  
2. **Format-Preserving Encryption (FPE):** For SSNs — maintains the valid format.

---

## ⚙️ Libraries and Setup

Install dependencies before running the script:

```bash
pip install faker cryptography pandas
````

**Requirements:**

* Python 3.8+
* Internet connection for Faker
* OS access for random key generation

**Imports:**

* `pandas` – Data handling
* `faker` – Synthetic data generation
* `cryptography` – Encryption primitives
* `os` – Random key generation

---

## 🧩 Code Purpose and Overview

This script demonstrates a **secure tokenization workflow**:

1. **Generate Synthetic Data:** Fake names and SSNs.
2. **Tokenize Sensitive Fields:** Encrypt while preserving structure.
3. **Export Tokenized Data:** Save to CSV for analysis or sharing.
4. **Manage Encryption Keys:** Save keys for reversible detokenization.

### 💡 Use Cases

* Data anonymization for ML datasets
* Secure sharing with third parties
* Testing compliance systems

---

## 🧾 Step-by-Step Code Walkthrough

```python
# Import required libraries
import pandas as pd
from faker import Faker
import os
from cryptography.fernet import Fernet
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms
from cryptography.hazmat.primitives import fpe
from cryptography.hazmat.backends import default_backend

# Initialize Faker to generate synthetic data
fake = Faker()

# Generate synthetic dataset
data = []
for _ in range(10):
    name = fake.name()
    ssn = fake.ssn()
    data.append({'name': name, 'ssn': ssn})

df = pd.DataFrame(data)

# Generate encryption keys
fernet_key = Fernet.generate_key()
fernet = Fernet(fernet_key)
aes_key = os.urandom(32)

# Function to tokenize names using Fernet
def tokenize_name(name):
    return fernet.encrypt(name.encode()).decode()

# Function to tokenize SSNs using Format-Preserving Encryption (FPE)
def tokenize_ssn(ssn):
    numerals = [int(d) for d in ssn.replace('-', '')]
    cipher = Cipher(
        algorithms.AES(aes_key),
        fpe.FF1(radix=10, max_tweak_len=0),
        backend=default_backend()
    )
    encryptor = cipher.encryptor()
    encrypted_numerals = encryptor.update(numerals) + encryptor.finalize()
    encrypted_ssn = ''.join(map(str, encrypted_numerals))
    return f"{encrypted_ssn[:3]}-{encrypted_ssn[3:5]}-{encrypted_ssn[5:]}"

# Apply tokenization
df['name'] = df['name'].apply(tokenize_name)
df['ssn'] = df['ssn'].apply(tokenize_ssn)

# Export tokenized data
df.to_csv('tokenized_data.csv', index=False)

# Save encryption keys
with open('keys.txt', 'w') as key_file:
    key_file.write(f"Fernet Key: {fernet_key.decode()}\n")
    key_file.write(f"AES Key (hex): {aes_key.hex()}\n")

print("Tokenized data exported to 'tokenized_data.csv'. Keys saved to 'keys.txt'.")
```

---

## 🧠 Breakdown of Key Sections

| Section                  | Description                                                  |
| ------------------------ | ------------------------------------------------------------ |
| **Data Generation**      | Uses Faker to create 10 fake PII records.                    |
| **Key Generation**       | Random Fernet and AES keys ensure unique encryption per run. |
| **Tokenize Name**        | Encrypts names with Fernet symmetric encryption.             |
| **Tokenize SSN**         | Uses AES FPE (FF1) mode to preserve SSN structure.           |
| **Application**          | Applies tokenization to all DataFrame rows.                  |
| **Export & Key Storage** | Saves tokenized data and encryption keys securely.           |

---

## 🧰 How to Run the Script

1. **Install Dependencies**

   ```bash
   pip install faker cryptography pandas
   ```
2. **Save the script**

   ```bash
   tokenize_pii.py
   ```
3. **Run**

   ```bash
   python tokenize_pii.py
   ```
4. **Output Files**

   * `tokenized_data.csv` — Tokenized dataset
   * `keys.txt` — Encryption keys

### 🔄 Detokenization Example

```python
fernet.decrypt(token.encode()).decode()
```

For SSNs, use the same AES key and FPE decryptor.

---

## 🧾 Example Output

```csv
name,ssn
gAAAAABm... (encrypted name),987-65-4321
```

---

## 🔐 Security Considerations & Best Practices

* **Key Security:** Store encryption keys securely (e.g., AWS KMS, HashiCorp Vault).
* **Never Hardcode Keys:** Always generate and manage dynamically.
* **Error Handling:** Use `try-except` for invalid data.
* **Performance:** Use multiprocessing for large datasets.
* **Regulatory Compliance:** Verify with GDPR/HIPAA/CCPA.
* **Ethical Use:** Protect user privacy and respect consent laws.

---

## 🧩 Limitations

* FPE may not be available in all cryptography libraries.
* Tokenization doesn’t prevent all data misuse.
* Combine with other measures like **access controls** and **data masking**.

---

## 🏁 Conclusion

Tokenization is an essential method for protecting PII like names and SSNs while retaining usability.
This lecture provided a **practical implementation** using Python’s cryptography library and **secure key handling**.

> 🔧 Try extending this example by adding more PII fields or implementing full detokenization logic.

---

**Thank you for attending this lecture on October 09, 2025!**
*Questions or feedback are always welcome.*

---


