# PawPals Pet Clinic - Log Investigation

## Project Overview
This project investigates sample SSH and Apache logs
to identify suspicious login attempts and web requests.

## SSH Findings
- 45.77.21.90: 3 failed login attempts
- 103.25.10.44: 3 failed login attempts
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
