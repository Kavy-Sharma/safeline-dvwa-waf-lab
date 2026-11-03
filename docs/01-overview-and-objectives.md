# 01 — Overview and Objectives

## Why I am building this lab

I wanted a small environment where I could work with a vulnerable web application and see what happens when a Web Application Firewall is placed in front of it. Reading about WAF rules is useful, but it is easier to understand their purpose when I can send a request, inspect the response and compare what happens before and after the WAF is configured.

The lab uses Kali Linux for testing, Ubuntu Server to host the application and SafeLine as the WAF. DVWA provides intentionally vulnerable features for controlled practice.

## What I want to learn

- How a web application is hosted and reached over a network.
- How a reverse proxy receives a client's request and forwards it to a backend server.
- How a WAF can inspect HTTP/HTTPS traffic and apply security rules.
- How local DNS/name resolution works in a small lab.
- What a self-signed TLS certificate does and what browsers mean when they do not trust it.
- How to test DVWA from Kali and review the WAF's detection or blocking behaviour.
- How to keep a useful record of each configuration and experiment.

## Role of each component

| Component | Role in this lab |
|---|---|
| VirtualBox | Runs the lab machines as virtual machines on the host |
| Kali Linux | Testing machine used to send requests and perform controlled security exercises |
| Ubuntu Server | Hosts DVWA and SafeLine |
| Apache | Serves the DVWA application on the backend |
| MySQL | Stores DVWA's application data |
| DVWA | Intentionally vulnerable web application used for learning |
| SafeLine WAF | Intended to sit in front of DVWA and inspect incoming web traffic |

## Intended traffic flow

Before the WAF is configured, Kali reaches the Apache/DVWA backend directly:

```text
Kali Linux (10.0.2.3)
        |
        | HTTP request to dvwa.local:8080
        v
Ubuntu Server (10.0.2.4)
        |
        v
Apache :8080
        |
        v
DVWA
```

The intended later arrangement is for the client to connect to SafeLine first. SafeLine will then forward permitted requests to the DVWA backend. That reverse-proxy path has not yet been verified, so it is shown as the target design rather than the current working path.

## Scope

The work is limited to the two virtual machines and the deliberately vulnerable DVWA application in this home lab. Any security testing described in later notes should be performed only against this lab or another system for which permission has been granted.

## Current state

The lab machines exist, DVWA is installed on Ubuntu, and Kali can resolve `dvwa.local` and reach the direct DVWA login endpoint on port `8080`. SafeLine is installed, but its application onboarding, certificate import and protection tests are still pending.

## Reference

The overall objectives and section order are based on *Safeline WAF Guide.pdf*, Section 1.

