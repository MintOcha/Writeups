## Overview on reverse shells: Types, uses, etc.

## Introduction
Rev shells, a type of virus, is a program that when ran on the target machine, establishes a connection between the target machine and a C2 (Command & Control) server. The target machine then receives commands (or, opens a shell) where the attacker could execute commands and gain RCE priveilages. Though this doesn't necessarily guarantee admin preveilages, the attacker could then use this RCE as a gateway to install spyware, other malware, disable windows defender, ransomware, and persistence software. They could also use your computer to further spread the worm, steal your personal information and compromise the local network.

Reverse shells payloads mainly come in a few different forms:
### Single-stage payload
This payload type usually involves a single executable or script that does the entire reversing. Can be sometimes detected via windows defender or other antiviruses. 

### Multi-stage payload
As its name suggests, it's a small executable or command that downloads another from a central server. The other executable is then responsible for the reverse shell connections. This is usually done with a delay to bypass antiviruses, or the first stage could be used to do checks for operating system type to download the appropriate 2nd stage payload.

## Payload formats

Payloads can appear as any formats, ranging from .docx (Word document), .xlsx (Excel sheet), .exe (Windows executable), .bat (Windows batch file) to .svg files (vector graphics) and even really unconventional ones. As long as it can run a script, it can contain a payload. There has recently been a rise on python and python computed payloads due to the popularity of the language. However, it's usually not popular due to the large compiled file size and slower nature among professionals.
There are existing C2 frameworks like metasploit, sliver c2 (the free ones) and numerous other private/paid ones (Like the leaked NSA documents by theshadowbrokers for example)

## Payload delivery
1.) Powershell script 
Read: [Reverse shell for windows powershell generation]|(https://github.com/Agisthemantobeat/Reverse-Shell-From-Word-Document)
Usually a 1 liner, pastable right into powershell (requires some physical access or a malicious USB drive)

2.) Executables/scripts
Not much variety here, just a standard script initiating a https/tcp connection between it and the c2 server. It could either run a continuous websocket or act as a beacon (periodic checking into the c2 server).
It's funny how most mobile device management networks are EXTREMELY similar in nature to a reverse shell network, hence a strong possibility of someone abusing those services as a rev shell (or, making their own so called device management service).


## C2 server options:
There are different c2 servers used by attackers, though many are bad options:

1.) a VPS (DO, Microsoft Azure), etc.
These public-ish servers are NOT a good choice for a c2 server as they are paid (so EVERYONE knows who u are) and it's also really against their TOS. 

2.) Self host: Port forwarding from a local device
ALSO not a good option since it involves exposing your home ip, also blatantly obvious. You can try covering it with a domain and cloudflare though. 
### Proxy options, without port forwarding
a.) Tailscale, a bit hard to setup and makes target implant bulky
b.) Ngrok, a free service (tho unreliable) that allows for port forwarding onto THEIR servers. (don't)
c.) Cloudflared and domain: Only allows 1 sided HTTP requests so the client needs to periodically check in with the server

There are probably more options like hosting on tor but lets not explore that in this writeup

## Recap

A reverse shell basically flows something like this:
1.) user downloads and runs malicious program
2.) Program establishes connection to remote server, via https or tcp or ws
3.) Attacker, listening on the server, is able to send commands and receive their output from the target device. They then usually steal your information (exfiltration), set it to start on startup (persistence) and more
