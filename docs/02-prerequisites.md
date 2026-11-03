# 02 — Prerequisites

## What the guide calls for

The reference guide lists these basic requirements:

- A host computer with at least 8 GB of RAM recommended.
- At least 50 GB of free disk space recommended.
- An internet connection to download the required software and updates.
- Basic Linux command-line familiarity for package installation and configuration-file editing.

The guide also expects VirtualBox, Kali Linux and Ubuntu Server images. DVWA and SafeLine are installed during later setup steps.

## Tools used in this lab

| Tool / component | Purpose |
|---|---|
| Oracle VirtualBox | Virtual machine management |
| Kali Linux | Security testing environment |
| Ubuntu Server | DVWA and SafeLine host |
| Apache2 | Web server for DVWA |
| MySQL | DVWA database |
| PHP and PHP MySQL support | DVWA application runtime and database connectivity |
| Git | Obtaining the DVWA source repository |
| OpenSSL | Required by the later self-signed certificate section |
| SafeLine | Web Application Firewall |

## Resource allocation notes

Ubuntu Server's memory allocation was increased from 4 GB to 6 GB after performance problems and system crash/problem notifications were observed. The VirtualBox display configuration was also adjusted:

- Graphics controller: VMSVGA
- Video memory: 128 MB
- 3D acceleration: disabled

These changes describe the current troubleshooting adjustments. They do not establish that the earlier stability issue has been permanently fixed.

The exact host RAM, host free disk space and Kali VM memory allocation have not been recorded in the project notes, so they are intentionally not stated here.

## Security note about credentials

The DVWA database account used in this lab is named `dvwa`. Its password is stored locally in the DVWA configuration. The real password is deliberately omitted from this public documentation. If a configuration example is needed, use a placeholder such as:

```php
$_DVWA[ 'db_user' ] = 'dvwa';
$_DVWA[ 'db_password' ] = 'REPLACE_WITH_YOUR_LOCAL_PASSWORD';
```

Do not commit the real password or any other secret to GitHub.

## Preparation checklist

- [x] VirtualBox installed and used to create the lab VMs.
- [x] Kali Linux VM created.
- [x] Ubuntu Server VM created.
- [x] Internet access was available for installation and setup work.
- [x] Ubuntu resource allocation adjusted to 6 GB RAM.
- [ ] Record exact host hardware and free disk space if these details are useful for reproducing the lab.

## Reference

*Safeline WAF Guide.pdf*, Section 2.

