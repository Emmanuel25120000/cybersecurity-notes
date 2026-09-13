# Cybersecurity Learning Notes

## 1. Cybersecurity Fundamentals

Cybersecurity is the practice of protecting computers, networks, applications, and data from unauthorized access, attacks, damage, or disruption.

### CIA Triad
- **Confidentiality** — only authorized people can access information.
- **Integrity** — information should not be altered without authorization.
- **Availability** — systems and information should be accessible when needed.

### Related security principles
- **AAA** — Authentication (proving who you are), Authorization (what you're allowed to do), Accounting (logging what you did).
- **Non-repudiation** — a party can't deny an action they took (e.g. digital signatures).
- **Defense in depth** — layering multiple security controls so no single failure exposes the whole system.
- **Principle of least privilege** — give a user or application only the permissions necessary to perform its job.
- **Zero trust** — never automatically trust a device or user, even inside the network perimeter; verify every request.

### Common security threats
- **Malware** — viruses, worms, trojans, ransomware, spyware, rootkits.
- **Phishing** — fraudulent messages tricking users into revealing info or installing malware. Variants: spear phishing (targeted), whaling (targets executives), smishing (SMS), vishing (voice).
- **Brute-force / credential stuffing** — guessing or replaying passwords, often using leaked credential lists.
- **Denial-of-Service (DoS) / Distributed DoS (DDoS)** — overwhelming a system so legitimate users can't use it.
- **Man-in-the-Middle (MITM)** — intercepting communication between two parties.
- **SQL Injection** — inserting malicious SQL through unsanitized input fields.
- **Cross-Site Scripting (XSS)** — injecting malicious scripts into web pages viewed by other users.
- **Social engineering** — manipulating people (not systems) into breaking security procedures.
- **Zero-day exploits** — attacks on vulnerabilities unknown to the vendor, with no patch yet available.
- **Privilege escalation** — gaining higher-level access than originally granted.
- **Unauthorized access** — accessing systems/data without permission.

### The threat landscape
Threat actors range from opportunistic script kiddies to organized cybercrime groups, hacktivists, insider threats, and state-sponsored actors. Understanding the *attacker's* motive (financial, political, espionage, disruption) shapes how a defender prioritizes controls.

---

## 2. Networking & Security

- **IP Address** — identifies a device or network interface on a network. Example: `192.168.1.10`
- **MAC Address** — hardware/network-interface identifier used for communication within a local network.
- **DNS** — Domain Name System, translates domain names into IP addresses.
  ```
  nslookup google.com
  ```
- **DHCP** — automatically provides network configuration such as IP address, subnet mask, default gateway, and DNS server.
- **SSH** — Secure Shell, allows secure remote access to another computer over a network.
  ```
  ssh username@192.168.1.10
  ```

### The OSI Model (7 layers)
A conceptual framework for how network communication happens, layer by layer:
1. **Physical** — cables, radio signals, raw bits.
2. **Data Link** — MAC addresses, switches (Ethernet).
3. **Network** — IP addresses, routing (routers).
4. **Transport** — TCP/UDP, ports, reliability.
5. **Session** — managing connections/sessions between apps.
6. **Presentation** — data formatting, encryption, compression.
7. **Application** — what the user interacts with (HTTP, FTP, DNS).

Knowing which layer an attack targets (e.g. ARP spoofing = Layer 2, DDoS = Layer 3/4, XSS = Layer 7) helps you pick the right defense.

### Ports & protocols worth knowing
| Port | Protocol | Use |
|---|---|---|
| 22 | SSH | Secure remote login |
| 23 | Telnet | Unencrypted remote login (legacy/insecure) |
| 53 | DNS | Domain resolution |
| 80 | HTTP | Web traffic (unencrypted) |
| 443 | HTTPS | Web traffic (encrypted, TLS) |
| 21 | FTP | File transfer (unencrypted) |
| 25 | SMTP | Email sending |
| 3389 | RDP | Windows remote desktop |

### Firewalls & VPNs
- **Firewall** — filters traffic in/out of a network based on rules (allow/deny by IP, port, protocol). Linux tools: `iptables`, `ufw`.
- **VPN (Virtual Private Network)** — encrypts traffic between a device and a remote server, hiding it from the local network/ISP and masking the real IP.
- **IDS/IPS** — Intrusion Detection/Prevention Systems monitor traffic for malicious patterns; IPS can actively block it.

---

## 3. Linux for Cybersecurity

Linux is widely used in cybersecurity because of its flexibility and powerful command-line tools.

| Command | Purpose |
|---|---|
| `pwd` | Shows the current directory |
| `ls` | Lists files and directories |
| `cd` | Changes directory |
| `cat` | Prints file contents |
| `chmod` | Changes file permissions |
| `chown` | Changes file ownership |
| `ps aux` | Lists running processes |
| `netstat -tulnp` / `ss -tulnp` | Shows open ports and listening services |
| `grep` | Searches text/logs for patterns |
| `top` / `htop` | Monitors system resource usage live |
| `journalctl` | Views systemd service logs |
| `tail -f /var/log/auth.log` | Watches login attempts live |

A useful workaround for pasting multi-line configs into a remote VPS shell without corruption: use a heredoc instead of a text editor —
```bash
cat > config.conf << 'EOF'
paste content here
EOF
```

---

## 4. Wireshark & Packet Analysis

Wireshark is a network protocol analyzer used to capture and inspect network traffic.

Useful packet information: source IP, destination IP, protocol, source/destination ports, packet contents, and TCP flags.

**TCP three-way handshake:**
```
Client → SYN     → Server
Client ← SYN-ACK ← Server
Client → ACK     → Server
```
Wireshark can be used to observe this process and understand how network communication works.

Useful Wireshark filters:
- `ip.addr == 192.168.1.10` — traffic to/from a specific IP
- `tcp.port == 22` — SSH traffic only
- `http` — HTTP traffic only
- `tcp.flags.syn == 1 && tcp.flags.ack == 0` — SYN packets only (can indicate a port scan)

---

## 5. Cybersecurity Tools

| Tool | Purpose |
|---|---|
| **Wireshark** | Captures and analyzes network traffic |
| **Nmap** | Network discovery and service/port enumeration |
| **Gobuster** | Brute-forces directories/files/subdomains on web servers |
| **Cowrie** | SSH/Telnet honeypot for observing unauthorized activity |
| **Metasploit** | Framework for developing and running exploits |
| **Burp Suite** | Intercepting proxy for testing web application security |
| **John the Ripper / Hashcat** | Password cracking (testing password strength) |
| **Hydra** | Online brute-force login testing |
| **Nginx** | Web server/reverse proxy, also used in security labs |
| **Docker** | Packages applications and dependencies into isolated containers |

Common Nmap usage:
```bash
nmap -sV -p- 192.168.1.10   # scan all ports, detect service versions
```

---

## 6. Honeypots

A honeypot is a system designed to attract and monitor suspicious or unauthorized activity.

```
Attacker
   ↓
Honeypot
   ↓
Logs activity
```

Cowrie is a medium/high-interaction SSH and Telnet honeypot. It can record login attempts, commands entered, connection information, files attackers attempt to download, and session activity.

The purpose is to observe and learn from attacker behavior, without exposing the real system.

**Applied example:** a honeypot deployed on a DigitalOcean VPS, built around a Rust WebSocket chat app behind an Nginx reverse proxy with systemd-managed services, sends real-time Discord alerts whenever suspicious traffic (e.g. Gobuster probing decoy paths) is detected. To keep alert volume manageable, features like per-IP alert cooldowns and automatic IP blocking help filter out noise from repeated automated scanner hits.

---

## 7. Web Security & Nginx

Nginx is commonly used as a web server and reverse proxy.

```
Client
   ↓
Nginx
   ↓
Application
```

A honeypot-style setup can use intentionally fake endpoints to study suspicious requests while keeping the actual application separate.

### OWASP Top 10 (common web vulnerabilities to know)
1. Broken access control
2. Cryptographic failures
3. Injection (SQL, command, etc.)
4. Insecure design
5. Security misconfiguration
6. Vulnerable/outdated components
7. Identification & authentication failures
8. Software/data integrity failures
9. Security logging & monitoring failures
10. Server-side request forgery (SSRF)

**Note:** Security testing should only be performed on systems you own or have explicit permission to test.

---

## 8. Docker Basics

Docker allows applications to run inside containers. A container packages an application together with the components it needs to run.

- **Image** — blueprint used to create a container.
- **Container** — running instance of an image.
- **Dockerfile** — instructions for building an image.
- **Port mapping** — makes a container service accessible from outside.

```
Host Machine
   ↓
 Docker
   ↓
 Container
   ↓
Application
```

Useful commands:
```bash
docker build -t myapp .
docker run -d -p 8080:80 myapp
docker ps            # list running containers
docker logs <id>     # view container logs
```

---

## 9. Cloud & DigitalOcean

Cloud platforms allow servers and other computing resources to be hosted remotely. DigitalOcean provides cloud infrastructure such as virtual machines called Droplets.

```
Local Computer
   ↓
 Internet
   ↓
Cloud Server
   ↓
Application
```

**Security considerations:** strong authentication, SSH key-based login (disable password auth), firewall configuration, keeping software updated, limiting exposed services, and monitoring logs.

---

## 10. Cryptography Basics

- **Encryption** — converting data into unreadable form; only someone with the correct key can reverse it.
  - **Symmetric encryption** — same key encrypts and decrypts (e.g. AES). Fast, but key distribution is a challenge.
  - **Asymmetric encryption** — public/private key pair (e.g. RSA). Public key encrypts, private key decrypts (or vice versa for signing).
- **Hashing** — one-way transformation of data into a fixed-length string (e.g. SHA-256). Used for verifying integrity and storing passwords (never store plaintext passwords).
- **Salting** — adding random data to a password before hashing, to defeat precomputed rainbow-table attacks.
- **TLS/SSL** — protocols that encrypt traffic between a client and server (what makes HTTPS secure).
- **Digital signatures** — use asymmetric crypto to prove a message came from a specific sender and wasn't altered.

---

## 11. Incident Response Basics

A general framework for handling a security incident:
1. **Preparation** — have tools, logging, and a response plan ready before anything happens.
2. **Identification** — detect and confirm that an incident occurred.
3. **Containment** — isolate affected systems to stop the spread.
4. **Eradication** — remove the root cause (malware, unauthorized access, etc.).
5. **Recovery** — restore systems to normal operation safely.
6. **Lessons learned** — document what happened and improve defenses.

---

## 12. Nigerian Cyber Law & Compliance

The **Cybercrimes (Prohibition, Prevention, etc.) Act 2015** is Nigeria's primary cybercrime legislation, covering offenses like unauthorized access, identity theft, cyberstalking, and electronic fraud. Knowing the legal landscape matters for understanding what's authorized ("explicit permission to test") versus a criminal offense.

---

## 13. Basic Security Practices

- Use strong, unique passwords (consider a password manager).
- Enable multi-factor authentication (MFA) where possible.
- Keep software updated.
- Do not expose unnecessary network services.
- Monitor logs.
- Back up important data.
- Be cautious with unknown links and files.
- Use the principle of least privilege.

---

## 14. Cybersecurity Career Paths

- **SOC Analyst** — monitors alerts/logs, triages incidents.
- **Penetration Tester / Ethical Hacker** — legally simulates attacks to find vulnerabilities.
- **Security Engineer** — builds and maintains defensive infrastructure.
- **Incident Responder** — handles active breaches.
- **GRC (Governance, Risk & Compliance)** — policy, audits, regulatory compliance.
- **Threat Intelligence Analyst** — tracks attacker groups, tactics, and trends.

---

## 15. Learning Progress

Currently building a foundation in cybersecurity through practical study and lab exercises. Coverage so far includes: networking fundamentals (OSI model, IP/DNS/DHCP/SSH), Linux, network traffic analysis (Wireshark), core cybersecurity concepts (CIA Triad, threat landscape, Nigerian cyber law), honeypots (Cowrie), Docker, Nginx, and cloud deployment on DigitalOcean — including a hands-on honeypot project combining a Rust WebSocket app, Nginx, systemd, and Discord-based alerting.

The focus is on understanding how systems communicate, how attacks can be observed, and how security tools can be used to investigate and protect systems.
