# Research Report: Common Network Security Threats

**Internship:** Oasis Infobyte (OIBSIP) – Security Analyst  
**Student:** Prince Quarcoo  
**Date:** September 2026

---

## 1. Introduction

Network security threats continue to grow in both frequency and sophistication. As organizations and individuals rely more on digital systems for communication, business, and critical services, attackers constantly look for ways to disrupt, intercept, or manipulate network traffic. Understanding the most common threats, how they work, and how to defend against them is essential for any network administrator or security professional. This report examines four major network security threats: Denial-of-Service (DoS/DDoS) attacks, Man-in-the-Middle (MITM) attacks, IP Spoofing, and DNS Poisoning/Spoofing. For each threat, the report explains the attack method, provides a real-world example, discusses the impact, and lists practical mitigation strategies.

---

## 2. DoS/DDoS Attacks

### How it works
A Denial-of-Service (DoS) attack aims to make a system or network unavailable to legitimate users by overwhelming it with traffic or requests. A Distributed Denial-of-Service (DDoS) attack is a more powerful version that uses many compromised devices (a botnet) to flood the target from multiple sources at the same time. Attackers often use amplification techniques (such as DNS or NTP reflection) to multiply the volume of traffic.

### Real-world example
In October 2016, the Mirai botnet launched a massive DDoS attack against Dyn, a major DNS provider. The attack reached traffic levels of over 1 Tbps and caused widespread outages across the eastern United States and Europe. Popular services including Twitter, Netflix, Reddit, Spotify, and PayPal became unreachable for many users for several hours.

### Impact
- Complete or partial service unavailability
- Financial losses due to downtime
- Damage to reputation and customer trust
- Possible secondary attacks while the target is distracted

### Mitigation strategies
1. Use cloud-based DDoS protection services (such as Cloudflare, AWS Shield, or Akamai) that can absorb large volumes of traffic.
2. Implement rate limiting and traffic filtering at the network edge and application layer.
3. Deploy anomaly detection systems that can identify unusual traffic patterns and automatically trigger defensive measures.

---

## 3. Man-in-the-Middle (MITM) Attacks

### How it works
In a Man-in-the-Middle attack, the attacker secretly positions themselves between two communicating parties. The attacker can intercept, read, modify, or inject data without either party knowing. Common methods include ARP spoofing on local networks, rogue Wi-Fi access points, and SSL/TLS certificate attacks.

### Real-world example
In 2011, the Dutch certificate authority DigiNotar was compromised. Attackers issued hundreds of fraudulent SSL certificates, including certificates for Google domains. This allowed them to perform MITM attacks on users, especially in Iran, and intercept encrypted traffic.

### Impact
- Theft of login credentials, personal data, and financial information
- Session hijacking
- Injection of malware or malicious content
- Loss of confidentiality and integrity of communications

### Mitigation strategies
1. Always use strong encryption (HTTPS/TLS) and enforce certificate pinning where possible.
2. Educate users to avoid public Wi-Fi for sensitive activities or to use a trusted VPN.
3. Implement network segmentation and monitor for unusual ARP or certificate activity.

---

## 4. IP Spoofing

### How it works
IP spoofing involves creating IP packets with a forged source address. The attacker makes the packet appear as if it came from a trusted or different source. This technique is frequently used to hide the attacker’s identity, bypass access controls, or amplify DDoS attacks (reflection/amplification).

### Real-world example
IP spoofing has been a core component of many large-scale DDoS attacks. A well-known historical case is the 1994–1995 attacks by Kevin Mitnick, who used TCP sequence prediction and IP spoofing to gain unauthorized access to systems. More recently, spoofed source addresses are routinely used in amplification attacks against major cloud providers and websites.

### Impact
- Enables anonymous attacks
- Bypasses IP-based access controls
- Makes attribution difficult
- Amplifies the power of DDoS attacks

### Mitigation strategies
1. Implement ingress and egress filtering (BCP 38) to block packets with spoofed source addresses.
2. Use reverse path forwarding (RPF) checks on routers.
3. Deploy firewalls and intrusion detection systems that can detect and drop suspicious spoofed traffic.

---

## 5. DNS Poisoning / DNS Spoofing

### How it works
DNS Poisoning (also called DNS cache poisoning or DNS spoofing) involves corrupting the DNS cache of a resolver so that it returns an incorrect IP address for a domain. When a user tries to visit a legitimate website, they are redirected to a malicious server controlled by the attacker.

### Real-world example
In 2008, security researcher Dan Kaminsky publicly demonstrated a serious flaw in the DNS protocol that made cache poisoning much easier. Although widespread exploitation was limited after rapid patching, the discovery forced the entire industry to improve DNS security. Later incidents, such as DNS hijacking attacks against banks and major websites, have shown the real-world danger of poisoned DNS responses.

### Impact
- Users are redirected to phishing or malware sites
- Credential theft and data interception
- Loss of trust in online services
- Potential for large-scale fraud

### Mitigation strategies
1. Deploy DNSSEC (Domain Name System Security Extensions) to authenticate DNS responses.
2. Use DNS resolvers that support random source ports and strong transaction IDs.
3. Regularly monitor DNS traffic and implement DNS filtering or protective DNS services.

---

## 6. Comparison Table

| Threat              | Attack Vector                  | Who is at Risk                  | Difficulty to Execute | Ease of Mitigation     |
|---------------------|--------------------------------|---------------------------------|-----------------------|------------------------|
| DoS/DDoS            | Network flooding / Botnets     | Any online service or website   | Medium to High        | Medium (with cloud protection) |
| Man-in-the-Middle   | Network positioning / Rogue AP | Users on shared or public networks | Medium              | Medium to High         |
| IP Spoofing         | Packet crafting                | Networks without filtering      | Medium                | High (with proper filtering) |
| DNS Poisoning       | DNS resolver / Cache           | Users and organizations relying on DNS | Medium to High   | Medium (with DNSSEC)   |

---

## 7. Conclusion – Key Takeaways for Network Administrators

1. **Defense in depth is essential** – No single control can stop all threats. Combining filtering, encryption, monitoring, and cloud protection provides much stronger security.

2. **Keep systems and protocols updated** – Many successful attacks exploit known weaknesses (such as unpatched DNS implementations or missing BCP 38 filtering). Regular updates and configuration hardening significantly reduce risk.

3. **Monitor and prepare for the unexpected** – Continuous network monitoring, anomaly detection, and having an incident response plan ready can limit the damage when an attack does occur.

Understanding these common threats and applying the recommended mitigations will help network administrators better protect their environments against both opportunistic and targeted attacks.

---

## 8. References

1. National Institute of Standards and Technology (NIST). Special Publication 800-61 – Computer Security Incident Handling Guide.  
2. Cybersecurity and Infrastructure Security Agency (CISA). Understanding Denial-of-Service Attacks. https://www.cisa.gov  
3. SANS Institute. Reading Room – Network Security Threats and Defenses.  
4. Krebs on Security. Coverage of major DDoS and related attacks (including the 2016 Mirai/Dyn incident).  
5. Cloudflare Learning Center and Blog – Technical explanations of DDoS, DNS attacks, and mitigation techniques.  
6. MITRE ATT&CK Framework – Techniques related to Network Denial of Service, Adversary-in-the-Middle, and spoofing.
