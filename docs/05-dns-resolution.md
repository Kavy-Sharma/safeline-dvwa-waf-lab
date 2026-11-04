# 05 — DNS Resolution Setup

## Goal

The purpose of this step is to let the lab use a readable hostname, `dvwa.local`, instead of typing Ubuntu's IP address every time.

For this small two-VM lab, the hostname is mapped locally through the `/etc/hosts` file. This is not a public DNS record and does not require running a DNS server.

## Method used: `/etc/hosts`

The mapping used in the lab is:

```text
10.0.2.4    dvwa.local
```

The entry maps the hostname `dvwa.local` to the Ubuntu Server address `10.0.2.4`.

The file can be edited with:

```bash
sudo nano /etc/hosts
```

Add the mapping on the machine that needs to resolve the name. The lab's working client-side resolution was verified from Kali. The guide describes adding the entry on both Ubuntu and Kali; the important requirement for the Kali-to-DVWA test is that Kali resolves `dvwa.local` to Ubuntu's address.

After editing, save the file and exit the editor. In Nano, `Ctrl+O` saves, `Enter` confirms the filename and `Ctrl+X` exits.

## Why BIND9 was not installed

The PDF labels Section 5.2, BIND DNS Server, as optional. A dedicated DNS service is unnecessary for the current two-machine exercise because a static local mapping is enough.

For that reason, BIND9 was not installed or configured. No BIND zone file or DNS resolver changes should be described as part of this lab.

The `/etc/hosts` method is simple, but it has a limitation: the mapping must be maintained on each machine that needs it. A dedicated DNS server becomes more useful when the lab grows or several machines need to resolve multiple lab hostnames centrally.

## Verification

From Kali, the following checks were used:

```bash
ping -c 4 10.0.2.4
```

This checks basic IP connectivity to Ubuntu. The lab's recorded result was successful.

```bash
getent hosts dvwa.local
```

This asks the system's name service configuration to resolve `dvwa.local`. The expected mapping is `10.0.2.4`.

The direct DVWA endpoint was then checked with:

```bash
curl -I http://dvwa.local:8080/DVWA/login.php
```

`curl` sends an HTTP request. The `-I` option requests response headers without downloading the full page body.

The observed response began with:

```text
HTTP/1.1 200 OK
```

This confirms that, at the time of the check, Kali could resolve the hostname and reach the DVWA login endpoint through Apache on port `8080`.

## What this result proves — and what it does not

It proves that:

- Kali can communicate with Ubuntu over the lab network.
- Kali can resolve `dvwa.local` to the Ubuntu address.
- The direct DVWA login endpoint responds successfully over HTTP on port `8080`.

It does not prove that:

- HTTPS is configured.
- The self-signed certificate exists.
- SafeLine is handling the request.
- The reverse-proxy route is working.
- Any WAF detection or blocking rule has been tested.

Those are later stages in the guide.

## Current DNS configuration summary

| Item | Value |
|---|---|
| Resolution method | Local `/etc/hosts` entry |
| Hostname | `dvwa.local` |
| Address | `10.0.2.4` |
| BIND9 | Not installed; optional section skipped |
| Direct application URL | `http://dvwa.local:8080/DVWA/login.php` |
| Last recorded HTTP result | `HTTP/1.1 200 OK` |

## Reference

*Safeline WAF Guide.pdf*, Section 5.1. Section 5.2 is explicitly optional and was not performed.

