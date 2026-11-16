# PawPals Pet Clinic - Log Investigation

## Project Overview
This project investigates sample SSH and Apache logs
to identify suspicious login attempts and web requests.

## SSH Findings
Nov 15 10:20:10 server sshd[108]: Failed password for root from 45.77.21.90 port 45628 ssh2
Nov 15 10:21:15 server sshd[109]: Failed password for invalid user guest from 103.25.10.44 port 45629 ssh2
- 192.168.1.105: Successful login for taha

## Apache Findings
- HTTP 200: Successful requests
- HTTP 403: Forbidden requests
- HTTP 500: Internal server errors
- Path traversal patterns: ../ and /etc/passwd

## Tools
- Kali Linux
- grep
- awk
- sort
- uniq
- Git

## Evidence
See notes.txt for commands and their outputs.

Note: The included logs are sample practice data,
not verified production incident evidence.
Repository status: Investigation documentation updated.
