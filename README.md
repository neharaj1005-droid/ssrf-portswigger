# SSRF PortSwigger

My write-ups from working through PortSwigger Web Security Academy SSRF labs, as part of building hands-on cybersecurity skills.

> **Scope & ethics note:** All labs in this repository are PortSwigger's own training environments, purpose-built and provisioned for learners to attack legally. No techniques here were used against systems I don't own or have explicit authorization to test. This repo is for educational and portfolio purposes only.

## What is SSRF?

Server-Side Request Forgery occurs when an attacker can induce a server-side application to make HTTP requests to an unintended location — often the server's internal network, localhost, or cloud metadata endpoints. It's dangerous because the request appears to originate from the trusted server itself, which can bypass firewalls, access controls, and network segmentation that would normally block an external attacker.

## Lab Index

| Lab | Difficulty | Status |
|-----|------------|--------|
| [Basic SSRF against the local server](ssrf-lab1-basic-ssrf-local-server) | Apprentice | ✅ Completed |
| [Basic SSRF against another back-end system](ssrf-lab2-another-backend-system) | Apprentice | ✅ Completed |
| [SSRF with blacklist-based input filter](ssrf-lab3-blacklist-filter) | Practitioner | ✅ Completed |
| SSRF with filter bypass via open redirection vulnerability | Practitioner | ⬜ Not started |
| SSRF with whitelist-based input filter | Practitioner | ⬜ Not started |
| Blind SSRF with out-of-band detection
cat > README.md << 'EOF'
# SSRF PortSwigger

My write-ups from working through PortSwigger Web Security Academy SSRF labs, as part of building hands-on cybersecurity skills.

> **Scope & ethics note:** All labs in this repository are PortSwigger's own training environments, purpose-built and provisioned for learners to attack legally. No techniques here were used against systems I don't own or have explicit authorization to test. This repo is for educational and portfolio purposes only.

## What is SSRF?

Server-Side Request Forgery occurs when an attacker can induce a server-side application to make HTTP requests to an unintended location — often the server's internal network, localhost, or cloud metadata endpoints. It's dangerous because the request appears to originate from the trusted server itself, which can bypass firewalls, access controls, and network segmentation that would normally block an external attacker.

## Lab Index

| Lab | Difficulty | Status |
|-----|------------|--------|
| [Basic SSRF against the local server](ssrf-lab1-basic-ssrf-local-server) | Apprentice | ✅ Completed |
| [Basic SSRF against another back-end system](ssrf-lab2-another-backend-system) | Apprentice | ✅ Completed |
| [SSRF with blacklist-based input filter](ssrf-lab3-blacklist-filter) | Practitioner | ✅ Completed |
| SSRF with filter bypass via open redirection vulnerability | Practitioner | ⬜ Not started |
| SSRF with whitelist-based input filter | Practitioner | ⬜ Not started |
| Blind SSRF with out-of-band detection | Practitioner | ⬜ Not started |
| Blind SSRF with Shellshock exploitation | Practitioner | ⬜ Not started |

## Repo structure

Each lab has its own folder containing a `README.md` write-up, a `screenshots/` folder with evidence, and a `.docx` version of the same report.

## Tools used

- Burp Suite (Community/Professional)
- Browser DevTools
- PortSwigger Web Security Academy lab instances

## Author

Neha Raj — MSc Cybersecurity
