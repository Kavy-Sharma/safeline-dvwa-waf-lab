# SafeLine WAF + DVWA Home Lab

A practical cybersecurity home lab built with VirtualBox, Kali Linux, Ubuntu Server, DVWA and SafeLine WAF. The purpose of the lab is to understand how a web application firewall works in front of a vulnerable web application, then observe how it handles controlled security tests.

This repository records the setup and experiments as they are completed. The notes describe the actual lab configuration rather than treating every instruction in the reference guide as if it had been followed exactly.

## Project goals

- Build an isolated environment for learning web application security.
- Understand the role of a reverse proxy and a Web Application Firewall (WAF).
- Configure a vulnerable application (DVWA) as a backend service.
- Learn how local name resolution and HTTPS certificates fit into the setup.
- Send controlled test requests from Kali Linux and observe the WAF's response.
- Keep a clear record of commands, configuration changes, verification steps and issues.

## Lab architecture

The current lab uses two VirtualBox virtual machines connected to the same NAT Network.

| Component | Current details |
|---|---|
| Attacker / testing machine | Kali Linux — `10.0.2.3` |
| Application / WAF machine | Ubuntu Server — `10.0.2.4` |
| VirtualBox network | `NatNetwork` — `10.0.2.0/24` |
| NAT Network gateway | `10.0.2.1` |
| DVWA web server | Apache on Ubuntu, port `8080` |
| DVWA path | `/var/www/html/DVWA` |
| Local hostname | `dvwa.local` |
| Direct backend URL | `http://dvwa.local:8080/DVWA/login.php` |
| SafeLine status | Installed; application onboarding and WAF policy configuration are later steps |

Ubuntu also has a SafeLine-related bridge address (`192.168.0.1`) and a Docker bridge address (`172.17.0.1`). These are separate interface/network addresses and should not be confused with the VirtualBox NAT Network addresses used for Kali-to-Ubuntu connectivity.

## Current progress

| PDF section | Topic | Status |
|---|---|---|
| 1 | Introduction | Reviewed |
| 2 | Prerequisites | Reviewed and lab requirements prepared |
| 3 | Lab Environment Setup | Kali and Ubuntu VMs created and networked |
| 4 | Ubuntu Server Configuration | Apache, MySQL and DVWA configured; DVWA setup page used to create/reset its database |
| 5 | DNS Resolution Setup | `/etc/hosts` mapping used; hostname resolution and direct HTTP access verified |
| 6 | Self-Signed SSL Certificate | Not started |
| 7 | Installing and Configuring SafeLine WAF | SafeLine is installed, but the guide's certificate import and DVWA onboarding steps are not yet completed |
| 8–10 | Security testing and conclusion | Not started |

The current direct DVWA check from Kali returned `HTTP/1.1 200 OK`:

```bash
curl -I http://dvwa.local:8080/DVWA/login.php
```

This confirms that the direct backend URL was reachable at the time of the test. It does **not** prove that requests are passing through SafeLine. The WAF reverse-proxy path will be configured and tested later.

## Repository layout

```text
safeline-dvwa-waf-lab/
├── README.md
├── docs/
│   ├── 01-overview-and-objectives.md
│   ├── 02-prerequisites.md
│   ├── 03-virtualbox-setup.md
│   ├── 04-ubuntu-dvwa-setup.md
│   └── 05-dns-resolution.md
├── screenshots/
├── diagrams/
└── scripts/
```

Screenshots, diagrams and scripts should be added when there is real evidence or a useful repeatable task to include. Do not add empty or misleading evidence just to make the folder look complete.

## Documentation approach

Each section records:
1. What the section is meant to achieve.
2. What was actually configured in this lab.
3. The relevant commands and what they do.
4. How the result was checked.
5. Any differences from the reference PDF, limitations or unresolved issues.

A command shown as a reference or verification command is not automatically proof that it was executed. Results are labelled as verified only when they were observed in the lab.

## Safety and scope

This is a deliberately vulnerable training environment. Keep it isolated to the lab network and use it only for systems you own or have explicit permission to test. Do not expose DVWA or its intentionally weak security settings to the public internet.

Do not commit passwords, API keys, private keys, session cookies, database dumps containing personal information or other secrets. Use placeholders in examples and keep real credentials in local configuration files excluded from Git.

## Reference guide

The lab follows the sequence in **Safeline WAF Guide.pdf**. The guide's optional BIND9 setup was not used; local name resolution was configured with `/etc/hosts` instead. The guide's Bridged Adapter recommendation was also not followed: this lab uses VirtualBox's `NatNetwork`.

## Next step

The documentation for sections 1–5 is being established first. The next practical section is **Section 6 — Creating a Self-Signed SSL Certificate**. It should begin only after the current documentation has been reviewed and committed.
