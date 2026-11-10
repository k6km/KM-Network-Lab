# KM Network Lab

A hands-on cybersecurity learning lab focused on Linux, network discovery, and traffic analysis.

## Lab Environment

| Machine | Role | IPv4 Address |
|---|---|---|
| Kali Linux | Scanning and packet analysis | 192.168.77.20 |
| Ubuntu Server | Lab target and SSH server | 192.168.77.10 |

Both virtual machines use the VirtualBox Internal Network named `KM-LAB`. All scans target my own lab systems.

## Exercise 1: SSH Service Discovery

### Objective

Discover a service from Kali, inspect its network traffic, and compare the results with listening sockets on Ubuntu.

### Commands Used

From Kali:

```bash
# Check connectivity
ping -c 4 192.168.77.10

# Scan selected TCP ports
nmap -n -p 22,80,443 192.168.77.10

# Scan the default 1,000 TCP ports
nmap -n 192.168.77.10

# Identify the SSH service and save a text report
nmap -n -sV -p 22 -oN ~/Desktop/KM-Practice/nmap-baseline.txt 192.168.77.10
```

From Ubuntu:

```bash
# Inspect listening TCP sockets and their processes
sudo ss -lntp
```

### Findings

- Ubuntu responded to all four ping requests.
- TCP port `22` was open; ports `80` and `443` were closed.
- The default scan found port `22` open and the other 999 scanned TCP ports closed.
- Service detection reported `OpenSSH 10.2p1 Ubuntu 2ubuntu3.5`.
- Ubuntu's socket listing confirmed SSH listening on `0.0.0.0:22` and `[::]:22`.
- Local DNS listeners used loopback addresses and were not exposed to Kali.

### Packet Analysis

I captured the selected-port scan on Kali's `eth0` interface using Wireshark.

The capture showed:

- ARP requests and replies resolving local IP addresses to MAC addresses.
- TCP SYN probes sent to ports `22`, `80`, and `443`.
- A SYN/ACK response from port `22`, indicating an open port.
- RST/ACK responses from ports `80` and `443`, indicating closed ports.
- A reset from Kali after the open-port response, ending the probe without completing the TCP handshake.

### Lessons Learned

- An open port indicates an available service, not proof of a vulnerability.
- A service's listening address affects whether other machines can reach it.
- Nmap shows what is reachable from the scanning machine; `ss` shows local listening sockets.
- Scanning 1,000 TCP ports does not cover every TCP port or any UDP ports.
- Service banners require further validation before making vulnerability claims.

## Evidence

The scan report and screenshots will be added under `reports/` and `screenshots/`.

## Scope

This repository documents beginner lab exercises. It does not claim a complete vulnerability assessment.
