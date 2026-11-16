# PawPals Pet Clinic - Log Investigation

## Project Overview
This project investigates sample SSH and Apache logs
to identify suspicious login attempts and web requests.

## SSH Findings
Command:

grep -Ei "Failed password|Invalid user" ssh.log

Nov 15 10:20:10 server sshd[108]: Failed password for root from 45.77.21.90 port 45628 ssh2
Nov 15 10:21:15 server sshd[109]: Failed password for invalid user guest from 103.25.10.44 port 45629 ssh2
- 192.168.1.105: Successful login for taha

## Apache Findings
Command:

grep "^45\.77\.21\.90 " apache.log

- HTTP 200: Successful requests
- HTTP 403: Forbidden requests
- HTTP 500: Internal server errors
- Path traversal patterns: ../ and /etc/passwd

## Path traversal patterns

Command:

grep -Ei '\.\./|%2e%2e|/etc/passwd' apache.log

Output (illustrative sample):

45.77.21.90 - - [15/Nov/2026:10:20:11 +0500] "GET /../../etc/passwd HTTP/1.1" 403 450
103.25.10.44 - - [15/Nov/2026:10:25:33 +0500] "GET /index.php?page=../../etc/passwd HTTP/1.1" 500 600

Finding: Both requests contain traversal indicators and target /etc/passwd. These are suspicious attempts to access a file outside the intended web directory.

## HTTP 403 and 500 responses

Command:

awk '$9 == 403 || $9 == 500' apache.log

Output (illustrative sample):

45.77.21.90 - - [15/Nov/2026:10:20:11 +0500] "GET /../../etc/passwd HTTP/1.1" 403 450
45.77.21.90 - - [15/Nov/2026:10:21:15 +0500] "GET /admin HTTP/1.1" 403 320
103.25.10.44 - - [15/Nov/2026:10:25:33 +0500] "GET /index.php?page=../../etc/passwd HTTP/1.1" 500 600

Finding: The two 403 responses show that access was forbidden for the requests from 45.77.21.90. The 500 response for 103.25.10.44 shows that the server encountered an internal error while processing the PHP request containing the traversal string. It makes the request worth investigating, but a 500 response alone does not establish that /etc/passwd was read or disclosed.

## Count failed SSH attempts by source IP

Command:

grep -Ei "Failed password|Invalid user" ssh.log | grep -oE '([0-9]{1,3}\.){3}[0-9]{1,3}' | sort | uniq -c | sort -nr

Output (illustrative sample):

3 45.77.21.90
3 103.25.10.44

Finding: In this sample, each IP generated three matching failed SSH login entries.

## Count path traversal requests by source IP

Command:

grep -Ei '\.\./|%2e%2e|/etc/passwd' apache.log | awk '{print $1}' | sort | uniq -c | sort -nr

Output (illustrative sample):

1 45.77.21.90
1 103.25.10.44

Finding: Each IP generated one matching path traversal request in this sample.

## Extract all HTTP 4xx and 5xx responses

Command:

awk '$9 ~ /^[45][0-9][0-9]$/' apache.log

Output (illustrative sample):

45.77.21.90 - - [15/Nov/2026:10:20:11 +0500] "GET /../../etc/passwd HTTP/1.1" 403 450
45.77.21.90 - - [15/Nov/2026:10:21:15 +0500] "GET /admin HTTP/1.1" 403 320
103.25.10.44 - - [15/Nov/2026:10:25:33 +0500] "GET /index.php?page=../../etc/passwd HT

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
