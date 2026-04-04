# Day 2 — Networking Fundamentals + Wireshark + First TryHackMe Room

Date: April 4, 2026
What I covered today: TCP/IP Model, Wireshark installation and live capture exercises, TryHackMe "What is Networking?" room

---

## How the day went

Honestly, today was one of those sessions where theory suddenly clicked because I could see it happening live on my screen. I watched the Professor Messer TCP/IP video first, took notes, and then immediately opened Wireshark. Seeing an actual DNS query and a real TCP handshake happen on my own computer while typing the commands that Professor Messer described — that was the moment it stopped being abstract.

---

## What I learned: TCP/IP Model

The TCP/IP model is the real-world, practical version of the OSI model. While OSI has 7 layers, TCP/IP consolidates them into 4:

- Network Access (covers OSI Layers 1 and 2 — physical hardware and MAC addresses)
- Internet (covers OSI Layer 3 — IP addresses, routing)
- Transport (covers OSI Layer 4 — TCP and UDP)
- Application (covers OSI Layers 5, 6, 7 — what software actually uses)

Everything that happens on the internet runs through this model every single time you send or receive anything.

---

## The TCP Three-Way Handshake — finally makes sense

Before today, I knew this phrase, but I did not really understand what it meant. Now I do.

When your computer wants to connect to a server — say, loading a website — it does not just start sending data. It goes through a three-step agreement first:

Step 1 — SYN: Your computer sends a synchronization packet. It is basically saying, "Hey, I want to connect. Here is my starting sequence number."

Step 2 — SYN-ACK: The server receives it and replies with a synchronization-acknowledgment. It is saying, "Got it, I agree to connect, here is MY starting sequence number."

Step 3 — ACK: Your computer sends a final acknowledgment back. "Perfect, we are both ready. Let's go."

Only after all three steps are complete does actual data start flowing.

Why this matters for security: The SYN Flood attack exploits exactly this. An attacker sends thousands of SYN packets but never sends the final ACK. The server keeps allocating memory waiting for responses that never come. Eventually the server runs out of resources and crashes. That is a denial of service attack, and it lives at Layer 4.

---

## TCP vs UDP — when does each one make sense?

TCP (Transmission Control Protocol)
- Reliable — guarantees every packet arrives
- Ordered — packets arrive in the right sequence
- Slower because of the handshake and acknowledgments
- Used for: websites (HTTP/HTTPS), email, file transfers, SSH

UDP (User Datagram Protocol)
- No handshake — just fires packets and hopes they arrive
- No guarantee of delivery or order
- Much faster than TCP
- Used for: DNS, video streaming, VoIP calls, online gaming

Real example: When you watch YouTube, your video uses UDP. If a few frames drop, you might see a glitch, but the video keeps playing. If it used TCP, one lost packet would pause the entire video waiting for a retransmit. UDP makes more sense for streaming.

---

## Wireshark — what I actually saw

I installed Wireshark today. Free from wireshark.org. After install I opened it, selected my WiFi interface, and started a capture.

### Exercise 1 — Watching DNS in real time

I set the filter to: dns

Then I opened my browser and typed bbc.com into the address bar.

In Wireshark, I immediately saw two packets appear:
- A Standard query — my computer asking "what is the IP address for bbc.com?"
- A Standard query response — the DNS server replying with the actual IP

The whole exchange took milliseconds. This happens every single time anyone types any website name anywhere. I never thought about it before, but it is constant and visible once you know where to look.

### Exercise 2 — Watching the TCP handshake

I cleared the filter and typed: tcp. flags.syn==1

Then loaded a new website. Found a [SYN] packet in the list, right-clicked it, and followed the TCP stream.

A window opened showing the full three-way handshake sequence. SYN from my machine. SYN-ACK from the server. ACK from my machine. Then the data started flowing.

This is the same thing Professor Messer described in the video. Seeing it live on actual traffic that I generated — completely different from just reading about it.

### Exercise 3 — Why HTTPS matters (reading HTTP in plain text)

I cleared the filter and typed: http

Then went to http://neverssl.com — a site that intentionally has no HTTPS.

I found an HTTP packet and clicked it. In the bottom panel of Wireshark, I could read the entire web request in plain text. Every header. Everything.

Write-up: If I were connected to a public WiFi and someone else on the network ran Wireshark while I used an HTTP site, they could see everything I sent. My login, my data, my session. HTTPS encrypts all of that, so even if someone captures the packets, they see nothing readable. This is exactly why every serious website uses HTTPS and why you should never use public WiFi without a VPN.

---

## TryHackMe — "What is Networking?" room completed

Room link: tryhackme.com/room/whatisnetworking
Status: 100% complete — all tasks done

### Key things I learned from this room

What is a network?
A network is any two or more devices that are connected and can communicate. Your phone and laptop on home WiFi is a network. The internet is just a massive interconnected collection of networks.

What is the internet?
The internet is not one thing owned by one organization. It is a global network of networks — millions of devices connected through agreed-upon protocols so they can all communicate even though they were built by different companies in different countries.

IP addresses
Every device on a network gets an IP address — a unique identifier that allows other devices to find it and send data to it.

- IPv4 looks like: 192.168.1.1 — 32 bits, allows about 4.3 billion unique addresses
- IPv6 looks like: 2001:0db8:85a3:0000:0000:8a2e:0370:7334 — 128 bits, allows an almost unlimited number of addresses. IPv6 was created because the world ran out of IPv4 addresses.

MAC addresses
Every network interface has a MAC (Media Access Control) address — a unique hardware identifier that cannot change. While IP addresses can change, MAC addresses are burned into the hardware. Format: six pairs of hexadecimal digits, like A4:C3:F0:85:AC:2D.

Ping and ICMP
Ping is a command that sends ICMP Echo Request packets to a destination and waits for an Echo Reply. It tells you two things:
1. Whether the device is reachable
2. How long the round trip takes (latency in milliseconds)

I ran ping google.com in my Kali terminal and got responses back with a latency of around 15ms. That means my packets are traveling from Newark, NJ, to a Google server and back in 15 thousandths of a second.

---

## Commands practiced today in Kali Linux

```
ping google.com
ping -c 4 google.com
```

The -c 4 flag tells ping to stop after 4 packets instead of running forever.

---

## What surprised me today

I expected networking to be dry and abstract. Instead, I found that once you can actually see the packets in Wireshark, it becomes genuinely interesting. You realize everything you do online — every click, every login, every message — is all just structured packets traveling through these protocols we looked at today.

The security implications start clicking, too. Once you see that HTTP traffic is readable in plain text, you immediately understand why attackers target public WiFi networks. Once you see how a TCP handshake works, you immediately understand how a SYN flood denial of service attack exploits it.

---

## Questions I still have

- How does a router decide which path to send a packet when there are multiple routes available?
- What exactly is inside a TLS certificate, and how does it prove a website is legitimate?
- How do attackers intercept WiFi traffic if it is encrypted?

These go on my list for future learning.

---

## Tomorrow — Day 3

- Professor Messer: Common Ports video + Introduction to IP
- First Nmap scan in Kali Linux on scanme.nmap.org
- TryHackMe: OSI Model room
- DNS recon commands: nslookup, dig, host
