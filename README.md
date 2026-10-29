## Hands-on with Hashing

---
---

### Overview

In this lab, I practiced generating and verifying file hashes using `md5sum` and `shasum`.

Hashing converts file contents into a fixed-length digest. Even a small change to a file can produce a different hash. Hash verification helps determine whether a file's contents match the data represented by a previously generated checksum.

I worked with three hashing algorithms:

* **MD5** — produces a 128-bit hash.
* **SHA-1** — produces a 160-bit hash.
* **SHA-256** — produces a 256-bit hash.

The lab demonstrated how to create checksum files, verify file integrity, modify a file, and observe verification failure.

---

### Tools & Resources

* Linux terminal
* `md5sum`
* `shasum`
* `nano`
* Text files
* Qwiklabs lab environment

### Lab Type

Linux / Cybersecurity / Cryptography Hands-On Lab

### Focus

Hashing, File Integrity, MD5, SHA-1, SHA-256, Hash Verification, Linux Command Line

---
---

### 1. Creating a Text File

First, I created a text file named `file.txt` containing a line of sample text.

```bash
echo "This is a sample text file." > file.txt
```

![1](https://i.imgur.com/mTjyXwy.png)

---
---

### 2. Generating an MD5 Hash

Generate an MD5 checksum and save it to a file:

```bash
md5sum file.txt > file.txt.md5
```

![2](https://i.imgur.com/ogOSo6Z.png)

View the saved checksum:

```bash
cat file.txt.md5
```

![3](https://i.imgur.com/R8ns0CS.png)

Example output from the lab:

```text
c7a8ef893898f9a6b380eb4ec1e87113  file.txt
```

The hash value depends on the exact contents of the file. My output may differ if I use a different message.

#### Verify the MD5 Checksum

Run:

```bash
md5sum -c file.txt.md5
```

![4](https://i.imgur.com/ycAjcZi.png)

Expected output when the file matches the saved checksum:

```text
file.txt: OK
```

> **Security note:** MD5 is considered cryptographically broken for collision resistance. It is useful for understanding checksum workflows, but it should not be relied on for security-sensitive integrity verification against deliberate attacks.

---
---

### 3. Creating and Modifying a Copy of the File

Create a copy of the original file:

```bash
cp file.txt badfile.txt
```

![5](https://i.imgur.com/m0iIdoF.png)

Generate an MD5 checksum for the copy:

```bash
md5sum badfile.txt
```

![6](https://i.imgur.com/JI08SAh.png)

If the copied file has exactly the same contents as the original, the hash values should match even though the filenames differ.

![7](https://i.imgur.com/gtuixjB.png)

**Key concept:** Hashes are calculated from file contents, not from the filename alone.

#### Modify the Copy

Open the copied file:

```bash
nano badfile.txt
```

In `nano`:

1. Use the arrow keys to move to the end of the text.
2. Add a single space at the end of the line.
3. Press `Ctrl-X` to exit.
4. Press `Y` to save the changes.
5. Press `Enter` to confirm the filename.

Adding a space changes the file contents.

#### Verify the Modified File

Check the original checksum against the modified file:

```bash
md5sum -c file.txt.md5
```

Because the saved checksum refers to `file.txt`, this command checks `file.txt`, not `badfile.txt`. To compare the modified copy against the original checksum, create a temporary checksum entry for `badfile.txt` using the original digest:

![8](https://i.imgur.com/egCk1qg.png)

The verification should report a failure because the modified file no longer matches the original digest.

Generate a new checksum for the modified file:

```bash
md5sum badfile.txt
```

![9](https://i.imgur.com/Lf8G0ev.png)

**What I learned:** A small change to file contents can produce a different digest and cause verification against the original checksum to fail.

---
---

### 4. Generating a SHA-1 Hash

The `shasum` utility can generate and verify SHA-family checksums.

Generate a SHA-1 checksum and save it:

```bash
shasum file.txt > file.txt.sha1
```

View the checksum:

```bash
cat file.txt.sha1
```

![9](https://i.imgur.com/AwqmPmS.png)

Expected output:

```text
file.txt: OK
```

**What I learned:** SHA-1 can be used to create and verify checksums, but it is no longer recommended for security-sensitive applications because of its weaknesses against collisions.

---
---

### 5. Generating a SHA-256 Hash

SHA-256 is another SHA-family algorithm and produces a 256-bit digest.

Generate a SHA-256 checksum and save it:

```bash
shasum -a 256 file.txt > file.txt.sha256
```

View the checksum:

```bash
cat file.txt.sha256
```

![10](https://i.imgur.com/Dr8Y7V3.png)

#### Verify the SHA-256 Checksum

Run:

```bash
shasum -a 256 -c file.txt.sha256
```

Expected output when the file matches the saved checksum:

```text
file.txt: OK
```

![11](https://i.imgur.com/FQxLkRv.png)

**What I learned:** SHA-256 produces a longer digest than MD5 or SHA-1 and is commonly used for modern file-integrity checks.

---
---

### 6. Comparing the Hashing Algorithms

| Algorithm | Digest size | Security considerations                                                          |
| --------- | ----------: | -------------------------------------------------------------------------------- |
| MD5       |    128 bits | Not suitable for security-sensitive collision resistance                         |
| SHA-1     |    160 bits | Deprecated for security-sensitive digital-signature and collision-resistant uses |
| SHA-256   |    256 bits | Common modern choice for file-integrity verification                             |

The key lesson is that a longer digest is not the only measure of security. The strength of the underlying algorithm also matters.

---
---

### Key Concepts Learned

* **Hashing:** Transforming data into a fixed-length digest.
* **Checksum:** A stored hash value used to check whether data matches an expected value.
* **Integrity verification:** Comparing a file's calculated hash with a previously recorded hash.
* **Hash mismatch:** A difference between the calculated and expected digest.
* **File modification:** Changes to file contents can produce a different digest.
* **Algorithm selection:** Modern security workflows should use a suitable, trusted algorithm such as SHA-256.

---
---

### Cybersecurity Relevance

Hashing is useful in many cybersecurity and IT support tasks:

* **File integrity monitoring:** Detect unexpected changes to files.
* **Incident response:** Compare file hashes against trusted reference values.
* **Malware analysis:** Use hashes to identify and compare suspicious files.
* **Software verification:** Check downloaded files against checksums published by a trusted source.
* **System administration:** Verify that files have not changed unexpectedly.

**Important limitation:** A matching hash only confirms that the file matches the checksum provided. If an attacker can modify both the file and the checksum, ordinary checksum verification alone may not detect the tampering. Use a trusted checksum source or a suitable digital-signature mechanism when authenticity matters.

---
---

### Troubleshooting Notes

| Problem                      | Possible cause or solution                                                 |
| ---------------------------- | -------------------------------------------------------------------------- |
| `file.txt: FAILED`           | The file may have changed, or the checksum may reference a different file. |
| Hashes differ unexpectedly   | Confirm the exact file contents, including spaces and newline characters.  |
| Checksum file is missing     | Regenerate the checksum and save it to the intended path.                  |
| Wrong algorithm used         | Use the same algorithm for generating and verifying the checksum.          |
| `shasum` command unavailable | Check whether the utility is installed in the environment.                 |

---
---

### Skills Demonstrated

* Linux command-line usage
* File creation and inspection
* MD5 checksum generation and verification
* SHA-1 and SHA-256 checksum generation and verification
* File-integrity checking
* Basic cryptographic concepts
* Troubleshooting checksum mismatches
* Cybersecurity awareness

---
---

### Final Takeaway

This lab demonstrated how hashing can help verify file integrity. By generating checksums, modifying a file, and repeating verification, I observed how changes to file contents affect the resulting digest.

I also learned that algorithm choice matters: MD5 and SHA-1 have important security weaknesses, while SHA-256 is a more appropriate choice for many modern integrity-checking workflows.
