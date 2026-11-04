# Section 6: Creating a Self-Signed SSL Certificate

## Objective

The objective of this section was to generate a self-signed SSL certificate and its corresponding private key for the DVWA application.

These files will be used later while configuring HTTPS through SafeLine WAF.

## 1. Checking OpenSSL

Before generating the certificate, I checked whether OpenSSL was installed on the Ubuntu Server.

Command:

```bash
openssl version
```

Output:

```text
OpenSSL 3.5.5 27 Jan 2026
```

OpenSSL was already installed and available.

## 2. Creating the Certificate Directory

Created a dedicated directory to store the SSL certificate and private key.

Command:

```bash
sudo mkdir -p /etc/ssl/dvwa
```

Verified the directory:

```bash
ls -ld /etc/ssl/dvwa
```

The directory was successfully created with root ownership.

## 3. Generating the Self-Signed Certificate

Used OpenSSL to generate a 2048-bit RSA private key and a self-signed X.509 certificate.

Command:

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/dvwa/dvwa.key \
-out /etc/ssl/dvwa/dvwa.crt
```

During certificate generation, the following details were provided:

| Field | Value |
|---|---|
| Country | IN |
| State | Rajasthan |
| Locality | Jodhpur |
| Organization | HomeLAb |
| Organizational Unit | Cybersecurity |
| Common Name | dvwa.local |
| Email | Left blank |

The certificate and private key were generated successfully.

### Understanding the command

- `req`: Creates and processes certificate requests.
- `-x509`: Generates a self-signed certificate.
- `-nodes`: Does not encrypt the private key with a passphrase.
- `-days 365`: Sets the certificate validity period to 365 days.
- `-newkey rsa:2048`: Generates a new 2048-bit RSA key.
- `-keyout`: Specifies where the private key is saved.
- `-out`: Specifies where the certificate is saved.

## 4. Verifying the Generated Files

Command:

```bash
sudo ls -l /etc/ssl/dvwa/
```

Verified files:

```text
dvwa.crt
dvwa.key
```

Observed permissions:

| File | Permissions | Purpose |
|---|---|---|
| dvwa.crt | 644 | Public certificate |
| dvwa.key | 600 | Private key |

The private key is accessible only to root, which helps protect it from unauthorized access.

**Important:** The private key must not be uploaded to GitHub or shared publicly.

## 5. Inspecting Certificate Details

Command:

```bash
sudo openssl x509 -in /etc/ssl/dvwa/dvwa.crt -noout -subject -issuer -dates
```

Verified details:

- Subject: `CN=dvwa.local`
- Issuer: Same as the subject.
- Valid from: 4 November 2026
- Expiry: 4 November 2027

The subject and issuer being identical confirms that the certificate is self-signed.

## 6. Verifying the Certificate and Private Key

To verify that the certificate and private key belong together, I calculated the SHA-256 hash of their corresponding public keys.

Certificate public key:

```bash
sudo openssl x509 -in /etc/ssl/dvwa/dvwa.crt -pubkey -noout | openssl sha256
```

Private key's derived public key:

```bash
sudo openssl pkey -in /etc/ssl/dvwa/dvwa.key -pubout | openssl sha256
```

Both commands returned the same SHA-256 hash:

```text
f39852431e4bf336960d49bb2c2811948a21f1df2b1d40da98314629f0f481bb
```

Matching hashes confirm that the certificate contains the public key corresponding to the generated private key.

## 7. Checking Subject Alternative Name (SAN)

Command:

```bash
sudo openssl x509 -in /etc/ssl/dvwa/dvwa.crt -noout -ext subjectAltName
```

Output:

```text
No extensions in certificate
```

This confirms that the generated certificate does not contain a Subject Alternative Name (SAN) extension.

The Common Name is `dvwa.local`, but modern browsers generally use SAN for hostname verification. This may need to be addressed if certificate hostname validation causes problems during later HTTPS testing.

## Result

- Generated a self-signed SSL certificate.
- Generated its corresponding RSA private key.
- Verified the certificate's subject, issuer and validity period.
- Confirmed that the certificate and private key match using SHA-256.
- Checked the certificate for a SAN extension and confirmed that it is absent.

**Certificate location:** `/etc/ssl/dvwa/dvwa.crt`

**Private key location:** `/etc/ssl/dvwa/dvwa.key`

The certificate and private key have been generated successfully on the Ubuntu Server. HTTPS configuration and SafeLine integration are still pending and will be covered in the following sections.

**Security note:** This is a self-signed certificate intended for an isolated learning lab. It is not a publicly trusted certificate.
