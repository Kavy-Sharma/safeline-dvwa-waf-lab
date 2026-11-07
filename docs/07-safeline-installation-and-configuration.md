# Section 7: Installing and Configuring SafeLine WAF

## Objective

The objective of this section was to verify the SafeLine WAF installation, import the self-signed SSL certificate, configure DVWA as a protected application and establish HTTPS communication through SafeLine.

The lab uses Kali Linux as the client and Ubuntu Server as the backend and SafeLine host.

## 1. Verifying SafeLine Installation

SafeLine was already installed on the Ubuntu Server, so I verified the existing Docker containers instead of running the installer again.

Command:

```bash
sudo docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

The following SafeLine containers were running:

- safeline-luigi
- safeline-mgt
- safeline-pg
- safeline-fvm
- safeline-detector
- safeline-chaos
- safeline-tengine

The management, PostgreSQL and detector containers reported healthy status.

The management dashboard was accessible from Kali at:

```text
https://10.0.2.4:9443
```

I successfully opened the dashboard and signed in.

## 2. Importing the SSL Certificate

The self-signed certificate and its private key had previously been generated on Ubuntu Server.

File locations:

```text
/etc/ssl/dvwa/dvwa.crt
/etc/ssl/dvwa/dvwa.key
```

Since the SafeLine dashboard was being accessed from Kali, I transferred the files using SCP over SSH.

First, I verified that the SSH service was running on Kali:

```bash
sudo systemctl status ssh
```

The service was active and listening on port 22.

From Ubuntu Server, I transferred both files:

```bash
sudo scp /etc/ssl/dvwa/dvwa.crt /etc/ssl/dvwa/dvwa.key kavy@10.0.2.3:/home/kavy/
```

Both files were transferred successfully.

On Kali, I restricted access to the private key:

```bash
chmod 600 ~/dvwa.key
```

The resulting file permissions were:

| File | Permissions |
|---|---|
| dvwa.crt | 644 |
| dvwa.key | 600 |

### Importing into SafeLine

In the SafeLine dashboard:

1. Opened the certificate management section.
2. Selected Add Cert.
3. Uploaded `dvwa.crt` as the SSL certificate.
4. Uploaded `dvwa.key` as the corresponding private key.
5. Submitted the certificate.

SafeLine displayed the imported certificate successfully.

Verified details:

- Certificate ID: 1
- Type: Uploaded
- Common Name / Issuer: dvwa.local
- Valid until: 4 November 2027
- Domain/IP setting: Match All Host

## 3. Configuring DVWA as an Application

Opened the Applications section in SafeLine.

An existing application named `DVWA Home Lab` was already present. I inspected and corrected its configuration rather than creating a duplicate.

### Initial configuration problems

The existing application had:

- Domain: `dvwa.local`
- Listening port: `8081`
- Protocol: HTTP
- Upstream: `http://10.0.2.4:80`

The upstream port was incorrect because Apache and DVWA were configured to use port 8080.

### Corrected configuration

The existing application was updated with the following settings:

| Setting | Final value |
|---|---|
| Application name | DVWA Home Lab |
| Domain | dvwa.local |
| Listening port | 443 |
| Protocol | HTTPS |
| SSL certificate | Uploaded certificate, ID 1 |
| Upstream | http://10.0.2.4:8080 |
| Health checks | Enabled |
| Health-check interval | 60 seconds |

The upstream address was accepted by SafeLine.

The configuration was submitted successfully.

## 4. Testing HTTPS Through SafeLine

After saving the application configuration, I tested the HTTPS endpoint from Kali Linux.

Command:

```bash
curl -k -I https://dvwa.local
```

Response:

```text
HTTP/1.1 200 OK
Content-Type: text/html
```

The request received a successful HTTP response through the SafeLine listener.

The response also contained a `sl-session` cookie, indicating that the response passed through SafeLine.

### Understanding the `-k` option

The certificate used in this lab is self-signed and is not trusted by default by the Kali browser or curl.

The `-k` option allows curl to continue without rejecting the certificate for that trust issue.

It is used here for isolated lab testing, not as a general recommendation for production environments.

## 5. Verifying the Actual DVWA Login Page

The DVWA installation is located inside the `/DVWA` directory, so the actual login page is:

```text
https://dvwa.local/DVWA/login.php
```

First, I checked the response headers:

```bash
curl -k -I https://dvwa.local/DVWA/login.php
```

Response:

```text
HTTP/1.1 200 OK
Content-Type: text/html;charset=utf-8
```

The response also included application session cookies:

- `PHPSESSID`
- `sl-session`

Next, I checked the returned HTML:

```bash
curl -k -s https://dvwa.local/DVWA/login.php | grep -i -E "DVWA|Damn Vulnerable"
```

Matching output:

```html
<title>Login :: Damn Vulnerable Web Application (DVWA)</title>
```

This confirmed that the actual DVWA login page was being served through the SafeLine HTTPS endpoint.

## 6. Final Traffic Flow

The configured lab traffic flow is:

```text
Kali Linux
    |
    | HTTPS request
    | https://dvwa.local/DVWA/login.php
    |
    v
SafeLine WAF
    |
    | HTTPS listener: 443
    | SSL certificate: dvwa.crt
    |
    | HTTP reverse proxy
    | http://10.0.2.4:8080
    |
    v
Ubuntu Server
    |
    v
Apache + DVWA
    |
    | HTTP backend: 8080
    v
DVWA Login Page
```

HTTPS terminates at SafeLine. The connection from SafeLine to the Apache backend uses HTTP within the isolated lab network.

## Result

The following activities were completed successfully:

- Verified the existing SafeLine Docker containers.
- Accessed and signed in to the SafeLine management dashboard.
- Transferred the SSL certificate and private key securely from Ubuntu to Kali using SCP.
- Restricted private-key file permissions.
- Imported the certificate into SafeLine.
- Inspected and corrected the existing DVWA application configuration.
- Configured HTTPS on port 443.
- Configured the backend upstream to `10.0.2.4:8080`.
- Received HTTP 200 OK through the SafeLine HTTPS endpoint.
- Confirmed that the actual DVWA login page was accessible through SafeLine.

**Important:** These tests establish successful HTTPS reverse-proxy connectivity. They do not yet prove that SafeLine detects or blocks SQL injection or other attacks. Attack detection and protection will be tested in the following sections.
