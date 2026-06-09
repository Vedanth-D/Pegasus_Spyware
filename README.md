# 🔍 Pegasus Spyware — Deep Research Report

> *A comprehensive technical and geopolitical analysis of the world's most sophisticated commercial surveillance tool.*

---

## Table of Contents

1. [Overview](#overview)
2. [History & Origins](#history--origins)
3. [How Pegasus Works](#how-pegasus-works)
4. [Infection Vectors](#infection-vectors)
5. [Capabilities & Data Collection](#capabilities--data-collection)
6. [Technical Architecture](#technical-architecture)
7. [Key Exploits & CVEs](#key-exploits--cves)
8. [Known Victims & Targets](#known-victims--targets)
9. [Geopolitical Impact](#geopolitical-impact)
10. [Legal Actions & Sanctions](#legal-actions--sanctions)
11. [Detection Methods](#detection-methods)
12. [Prevention & Mitigation](#prevention--mitigation)
13. [The Broader Spyware Ecosystem](#the-broader-spyware-ecosystem)
14. [Ethical & Human Rights Concerns](#ethical--human-rights-concerns)
15. [Timeline of Key Events](#timeline-of-key-events)
16. [References & Further Reading](#references--further-reading)

---

## Overview

**Pegasus** is a military-grade surveillance spyware developed by **NSO Group**, an Israeli cyber-intelligence company founded in 2010. It is widely regarded as the most sophisticated commercial spyware ever documented. Pegasus is capable of completely compromising both iOS and Android smartphones — silently, remotely, and often without any interaction from the target device's owner.

NSO Group markets Pegasus exclusively to government agencies under the premise that it is used to combat terrorism, organized crime, and child trafficking. However, extensive forensic investigations by Amnesty International, Citizen Lab, and other organizations have revealed systematic misuse against journalists, human rights defenders, lawyers, opposition politicians, and heads of state.

> **"Short of not using a device, there is no way to prevent exploitation by a zero-click exploit. It's a weapon against which there is no defense."**
> — Google Project Zero Researchers, 2021

---

## History & Origins

| Year | Event |
|------|-------|
| 2010 | NSO Group founded in Israel by Niv Carmi, Shalev Hulio, and Omri Lavie |
| 2011 | First known Pegasus build developed |
| 2016 | **Trident** exploit chain discovered; first public documentation of Pegasus (Ahmed Mansoor case) |
| 2017 | Zero-click attacks emerge |
| 2019 | WhatsApp zero-click exploit targets ~1,400 users globally |
| 2021 | **Pegasus Project** — consortium of 17 media outlets publishes leaked list of 50,000+ potential targets |
| 2021 | **ForcedEntry** exploit bypasses Apple's BlastDoor sandbox on iOS 14.6 |
| 2023 | **BLASTPASS** exploit chain hits iOS 16.6 via PassKit payloads |
| 2024 | WhatsApp awarded $167.7M in damages against NSO Group |
| 2024 | Pegasus confirmed in use against civil society in Jordan and Togo |

NSO Group has gone through several ownership changes over the years. The company was acquired by Francisco Partners in 2014 and later reacquired by its founders. It has operated under names including **Q Cyber Technologies** and **OSY Technologies**.

---

## How Pegasus Works

Pegasus operates in three broad phases: **Infiltration → Persistence → Exfiltration**.

### Phase 1: Infiltration

The operator selects a target and deploys one of several delivery mechanisms. Early versions required a target to click a malicious link (spear-phishing). Modern versions use **zero-click exploits** — the device is compromised with absolutely no interaction required from the victim. In some cases, even simply *receiving* a specially crafted message is sufficient.

### Phase 2: Persistence & Privilege Escalation

Once the initial exploit runs, Pegasus roots itself into the device by exploiting OS-level vulnerabilities to gain kernel-level access. It buries itself in core system files, making removal extremely difficult. It operates silently and disguises its network traffic as routine system communication.

### Phase 3: Exfiltration

Pegasus copies targeted data from the device, compresses it, encrypts it using **AES 128-bit encryption**, and transmits it to a command-and-control (C2) server operated by the NSO customer. Operators can:

- Collect all available data from the device
- Configure selective retrieval (specific app, contact, location zone)
- Set trigger conditions (e.g., "send alert when target arrives at location X")
- Activate live microphone or camera surveillance on demand

---

## Infection Vectors

### 1. Zero-Click Exploits (Most Advanced)
No user interaction required. Pegasus exploits vulnerabilities in:
- **iMessage** (Apple's messaging platform)
- **WhatsApp** (Meta's messaging app)
- **Apple Music** (media-processing pipeline)
- **PassKit** (Apple's wallet/pass framework)

A malformed message, attachment, or invisible push notification triggers the exploit automatically upon receipt.

### 2. One-Click Exploits (Spear-Phishing)
Earlier delivery method — still used in some campaigns. A target receives a malicious link via:
- SMS / iMessage
- WhatsApp
- Email

Clicking the link triggers a browser or app exploit chain that installs Pegasus. Targets are often sent convincing context ("Your package is delayed — track here").

### 3. Network Injection (Passive MITM)
Pegasus can be delivered via rogue cellular network equipment (IMSI catchers / "stingrays"). When a target browses an unencrypted HTTP site, the network operator silently injects a redirect that delivers the Pegasus payload without the user visiting any malicious link.

### 4. Physical Access
In some documented cases, Pegasus was installed with direct physical access to an unlocked device. This is less common given the sophistication of remote vectors.

---

## Capabilities & Data Collection

Once installed, Pegasus grants near-total access to the device. Documented capabilities include:

| Category | What Pegasus Can Do |
|----------|-------------------|
| **Messages** | Read SMS, iMessage, WhatsApp, Telegram, Signal, WeChat, Viber (including end-to-end encrypted messages by reading them before/after encryption) |
| **Calls** | Access call logs; record VoIP and cellular calls in real time |
| **Email** | Access all email accounts configured on device |
| **Microphone** | Activate microphone silently to record ambient audio |
| **Camera** | Activate front and rear cameras to take photos/video |
| **Location** | Track real-time GPS coordinates and movement history |
| **Passwords** | Log all keystrokes; harvest saved passwords and credentials |
| **Contacts** | Export full contacts list and relationship graph |
| **Photos & Files** | Access gallery, documents, and app data |
| **Browsing History** | Retrieve full browsing history and bookmarks |
| **App Data** | Access data from any app installed on the device |
| **Encrypted Comms** | Circumvent encryption by reading data directly from memory |

> **Critical point**: Pegasus bypasses end-to-end encryption not by breaking the encryption itself, but by compromising the device and reading data directly from the device's memory — before it is encrypted (outgoing) or after it is decrypted (incoming).

---

## Technical Architecture

### Command & Control Infrastructure
Pegasus uses a distributed C2 infrastructure consisting of:
- **Relay servers** in multiple countries to obscure traffic origin
- **Anonymizing proxies** to prevent attribution back to the operator
- Domains mimicking legitimate services (e.g., hosting, CDN providers)

Amnesty International's forensic team identified a distinctive network fingerprint used across Pegasus campaigns by scanning internet-wide infrastructure, enabling them to map NSO Group's global C2 network.

### Evasion & Anti-Forensics
- **Code obfuscation**: Pegasus source code and binaries are heavily obfuscated
- **Self-destruct**: The spyware can remotely wipe itself from a device, leaving minimal forensic traces
- **Encrypted communication**: All data exfiltration is encrypted, making traffic indistinguishable from legitimate HTTPS
- **Mimicry**: Pegasus disguises its processes under names resembling legitimate system services
- **Anti-debugging**: Detects if it's being analyzed in a sandboxed/emulated environment and halts execution

### Data Flow
```
Target Device → Compression (zlib) → Encryption (AES-128) → Relay Servers → NSO Customer C2 Panel
```

---

## Key Exploits & CVEs

### Trident (2016)
Three chained iOS zero-days discovered after being used against UAE activist Ahmed Mansoor:
- **CVE-2016-4657** — WebKit memory corruption via Safari
- **CVE-2016-4655** — Kernel memory disclosure
- **CVE-2016-4656** — Kernel use-after-free allowing jailbreak

### FORCEDENTRY (2021)
Considered one of the most sophisticated exploits ever publicly analyzed. A zero-click attack delivered via iMessage:
- Exploited the **JBIG2** image compression parser in CoreGraphics
- Bypassed Apple's **BlastDoor** sandbox — a mitigation specifically added to block this class of attack
- Google Project Zero researchers called it rivaling exploits accessible "only to a handful of nation states"
- Patched in iOS 14.8 (emergency release, September 2021)

### WhatsApp Exploits — Heaven, Eden, Erised (2019–2020)
Three successive exploit chains targeting WhatsApp's signaling infrastructure:
- **Heaven**: Exploited WhatsApp relay servers
- **Eden**: Improved version; required passing through relay servers to ensure compatibility
- **Erised**: Final zero-click iteration; blocked by WhatsApp in May 2020 after the lawsuit was filed

These exploits affected approximately **1,400 WhatsApp users** in a single two-week campaign.

### BLASTPASS (2023)
Zero-click exploit chain targeting iOS 16.6:
- Delivered via **PassKit** (Apple Wallet) payloads through iMessage
- No user interaction required; exploited image-rendering pipeline
- Patched in iOS 16.6.1 (emergency release)

---

## Known Victims & Targets

The 2021 **Pegasus Project** investigation, involving 17 media outlets and Forbidden Stories, leaked a list of over **50,000 phone numbers** believed to have been selected for targeting by NSO Group customers. Key confirmed victims include:

### Heads of State & Politicians
- **Emmanuel Macron** (French President) — listed as potential target
- **Imran Khan** (Former Pakistani Prime Minister)
- **Cyril Ramaphosa** (South African President)
- Multiple other sitting heads of government

### Journalists
- Employees of **El Faro** (Central American investigative outlet) — 22 staff infected between 2020–2021
- Reporters at major outlets including Reuters, AP, CNN, NYT
- At least **180 journalists** identified in the leaked list

### Human Rights Defenders
- **Lama Fakih** (Human Rights Watch) — infected 5 times between April–August 2021
- **Ahmed Mansoor** (UAE activist) — his targeting in 2016 led to the first public discovery of Pegasus
- Lawyers, counselors, and dissidents across Bahrain, Saudi Arabia, Mexico, and India

### Royalty
- **Princess Haya** of Dubai — confirmed by NSO Group's own CEO in a legal deposition

### Law Enforcement (Controversial)
- The FBI reportedly purchased a Pegasus license in 2019, ostensibly for research, though internal documents revealed attempted operational use in 2020–2021

---

## Geopolitical Impact

NSO Group identified potential customers in at least **50 countries**, including states with documented records of press suppression and human rights abuses:

**Confirmed/suspected client governments**: Azerbaijan, Bahrain, Hungary, India, Kazakhstan, Mexico, Morocco, Rwanda, Saudi Arabia, Togo, UAE, and others.

### India
Pegasus was found on devices belonging to opposition politicians, journalists, and activists during politically sensitive periods. The Indian government neither confirmed nor denied use, triggering a Supreme Court inquiry.

### Saudi Arabia
Pegasus was reportedly used to monitor the inner circle of **Jamal Khashoggi**, the Washington Post journalist murdered in October 2018. While direct causal links remain debated, forensic evidence showed Pegasus on devices of those close to him.

### Hungary
Hungary became the only EU member state confirmed to have deployed Pegasus against its own citizens — targeting investigative journalists and critics of the government of Prime Minister Viktor Orbán.

### Mexico
Extensive deployment against journalists, anti-corruption investigators, and health advocates. One of the most documented cases of widespread domestic misuse.

---

## Legal Actions & Sanctions

### United States — Entity List (November 2021)
The US Department of Commerce added NSO Group to its **Entity List**, effectively banning American companies from selling technology to NSO Group without a special license. This cut off NSO from using US cloud infrastructure, security tools, and software.

### WhatsApp v. NSO Group (2019–2024)
- Meta/WhatsApp filed suit in 2019 under the Computer Fraud and Abuse Act (CFAA)
- In **December 2024**, a US federal judge found NSO Group liable
- NSO Group was ordered to pay **$167.7 million in damages**
- Court documents revealed NSO employees personally operated Pegasus attacks — contradicting the company's long-held claim that it only provides tools and clients operate independently

### Apple v. NSO Group (2021)
Apple filed suit in November 2021 seeking a permanent injunction to prevent NSO Group from using any Apple devices, services, or software. Apple also committed $10 million to cybersurveillance research.

### European Union — PEGA Committee (2022)
The European Parliament formed a special committee (PEGA) to investigate Pegasus use within EU member states. The committee recommended export controls and a moratorium on commercial spyware pending regulatory frameworks.

### United Nations (2023)
UN human rights experts called for a **global moratorium** on the sale and use of commercial surveillance technology until adequate international safeguards are in place.

---

## Detection Methods

Due to Pegasus's sophisticated anti-forensic capabilities, traditional antivirus tools cannot reliably detect it. The following specialized approaches have been developed:

### Mobile Verification Toolkit (MVT)
- Developed by **Amnesty International Security Lab**
- Open-source, available on GitHub
- Analyzes iPhone backups, sysdiagnose archives, and Android device logs for known **Indicators of Compromise (IoCs)**
- Requires command-line familiarity and Python knowledge
- Most effective for iOS; Android functionality is more limited (analyzes APKs and SMS only)
- Regularly updated with new IoC lists from confirmed cases

**Basic MVT workflow (iOS)**:
1. Create an encrypted backup of the iPhone to a computer
2. Install MVT (`pip install mvt`)
3. Run `mvt-ios check-backup --iocs pegasus.stix2 /path/to/backup`
4. Review output for flagged indicators

### Kaspersky iShutdown Method (2024)
Kaspersky researchers discovered that Pegasus leaves traces in **iOS shutdown logs** (`/private/var/db/diagnostics/shutdown.log`). Unusual processes that persist across reboots — particularly those linked to known Pegasus infrastructure — can indicate infection. Kaspersky released `iShutdown.py` to automate this analysis.

### Apple Lockdown Mode Indicators
Apple's Lockdown Mode (iOS 16+) significantly raises the bar for zero-click attacks but does not guarantee protection. If attacks fail against Lockdown Mode-enabled devices, this itself can indicate targeting attempts were made.

### Behavioral Indicators (Imprecise)
While Pegasus is designed to be invisible, high-volume infections may occasionally produce:
- Unexpected battery drain
- Unusual data usage spikes
- Device overheating
- Sluggish performance

These are not reliable indicators — Pegasus is specifically engineered to minimize resource usage.

---

## Prevention & Mitigation

### High-Priority Actions

**1. Keep software updated — immediately**
Most Pegasus exploits target known vulnerabilities for which patches have been issued. Installing updates immediately after release — especially emergency/out-of-band patches — is the single most effective defense.

**2. Enable Lockdown Mode (iOS 16+)**
Apple introduced Lockdown Mode specifically in response to Pegasus-class threats. It restricts:
- Most message attachment types in iMessage
- Complex web technologies in Safari
- Incoming FaceTime calls from unknown contacts
- Wired connections when locked

Recommended for journalists, activists, lawyers, and others at elevated risk.

**3. Reboot your device regularly**
Pegasus in some forms is not fully persistent across reboots. Regular reboots can disrupt active surveillance sessions, though they do not guarantee removal.

**4. Be wary of all messages**
Avoid opening messages or attachments from unknown senders. Though zero-click attacks require no interaction, one-click attacks remain common. Do not click links in SMS, WhatsApp, or email — even from known contacts if the message seems unusual.

**5. Minimize app permissions**
Restrict camera, microphone, location, and contacts access for apps that don't genuinely require them.

**6. Use encrypted, disappearing messages**
Disappearing messages in Signal reduce the volume of data available if a device is later compromised.

### If You Suspect Infection

1. Do **not** continue using the compromised device for sensitive communications
2. Contact a digital security expert or organization (Citizen Lab, Access Now, EFF)
3. Run MVT analysis on device backup
4. Consider a **factory reset** — though restoring from a backup may reintroduce the infection
5. If targeted for political reasons, contact **Amnesty International** or **Human Rights Watch**

> ⚠️ **Important**: Performing a factory reset may remove some versions of Pegasus but does not guarantee complete removal. NSO Group has developed variants that survive factory resets by exploiting firmware-level persistence.

---

## The Broader Spyware Ecosystem

Pegasus is the most documented example of a growing commercial spyware industry. Similar tools have emerged from other vendors:

| Tool | Developer | Country |
|------|-----------|---------|
| **Predator** | Cytrox / Intellexa | Greece/North Macedonia |
| **Hermit** | RCS Lab | Italy |
| **Reign** | QuaDream | Israel |
| **Graphite** | Paragon Solutions | Israel |
| **FinFisher** | Gamma Group | Germany/UK |

In February 2025, **Paragon's Graphite** spyware was found targeting approximately 90 journalists via a WhatsApp zero-click campaign — demonstrating that even after Pegasus-era scrutiny, zero-click commercial spyware proliferation continues.

---

## Ethical & Human Rights Concerns

The Pegasus scandal raises fundamental questions about the commercial surveillance industry:

### The "Lawful Intercept" Argument
NSO Group and similar vendors argue their tools are sold only for lawful interception — fighting terrorism, organized crime, and child trafficking. However, the documented record shows consistent misuse against exactly the populations such surveillance should protect: journalists exposing corruption, human rights defenders documenting abuses, and political opposition.

### The Dual-Use Problem
Unlike a firearm or a chemical weapon, surveillance software has legitimate uses. The challenge is creating meaningful oversight to prevent abuse without eliminating legitimate law enforcement capabilities — a balance no country has yet struck successfully.

### Accountability Gap
Vendors like NSO Group claim no responsibility for how clients use their tools, drawing an analogy to arms manufacturers. Courts have begun to reject this framing — the WhatsApp ruling established that when NSO Group employees personally operate attacks on behalf of clients, the company cannot claim to be merely a passive toolmaker.

### Chilling Effects
Even the *possibility* of Pegasus surveillance has documented chilling effects: journalists self-censor, activists change behavior, lawyers worry about attorney-client privilege. The surveillance doesn't need to be active to be harmful.

### Democracy & Rule of Law
The use of Pegasus by Hungary — an EU member state — against journalists and opposition figures illustrates how surveillance tools can be weaponized to undermine democratic institutions from within.

---

## Timeline of Key Events

```
2010  ──── NSO Group founded in Israel
2011  ──── First Pegasus build developed
2016  ──── Trident exploit chain exposed via Ahmed Mansoor case (Citizen Lab)
2017  ──── Zero-click attack capability deployed
2018  ──── Pegasus linked to Jamal Khashoggi surveillance network
2019  ──── WhatsApp exploit hits ~1,400 users; Meta files lawsuit
2020  ──── WhatsApp blocks Erised exploit; Eden previously deployed
2021  ──── Pegasus Project: 17 media outlets expose 50,000+ targets
         ──── ForcedEntry exploit bypasses Apple BlastDoor
         ──── US adds NSO Group to Entity List
         ──── Apple files lawsuit; releases Lockdown Mode plans
         ──── Apple releases emergency iOS 14.8 patch
2022  ──── EU PEGA Committee formed to investigate member state abuses
         ──── Hungary confirmed using Pegasus against journalists
2023  ──── BLASTPASS zero-click exploit chain on iOS 16.6
         ──── UN calls for global moratorium on commercial spyware
         ──── Kaspersky releases iShutdown.py detection tool
2024  ──── WhatsApp wins $167.7M in damages vs NSO Group
         ──── Pegasus confirmed in Jordan and Togo operations
         ──── Court documents reveal NSO employees operated attacks directly
2025  ──── Paragon Graphite targets ~90 journalists via WhatsApp zero-click
         ──── Courts move to block NSO from targeting WhatsApp users further
```

---

## References & Further Reading

### Primary Research
- [Amnesty International — Forensic Methodology Report (2021)](https://www.amnesty.org/en/latest/research/2021/07/forensic-methodology-report-how-to-catch-nso-groups-pegasus/)
- [Citizen Lab — The Million Dollar Dissident (2016)](https://citizenlab.ca/2016/08/million-dollar-dissident-iphone-zero-day-nso-group-uae/)
- [Google Project Zero — FORCEDENTRY Deep Dive (2021)](https://googleprojectzero.blogspot.com/2021/12/a-look-at-nso-groups-pegasus-spyware.html)
- [ArXiv — Comprehensive Analysis of Pegasus (2024)](https://arxiv.org/abs/2404.19677)

### Tools
- [Mobile Verification Toolkit (MVT)](https://github.com/mvt-project/mvt) — Amnesty International
- [Kaspersky iShutdown.py](https://www.kaspersky.com/blog/how-to-protect-from-pegasus-spyware/43453/) — iOS shutdown log analyzer

### News & Investigations
- [The Pegasus Project — Forbidden Stories Consortium](https://forbiddenstories.org/case/the-pegasus-project/)
- [NSO Group Court Documents — TechCrunch (2024)](https://techcrunch.com/2024/11/15/nso-group-admits-cutting-off-10-customers-because-they-abused-its-pegasus-spyware-say-unsealed-court-documents/)
- [WhatsApp Wins $167.7M — The Guardian (2024)](https://www.theguardian.com)

### Organizational Resources
- [Citizen Lab](https://citizenlab.ca) — University of Toronto interdisciplinary lab
- [Access Now Digital Security Helpline](https://www.accessnow.org/help/) — for journalists/activists under threat
- [EFF Surveillance Self-Defense](https://ssd.eff.org)

---

*This document is intended for educational, research, and cybersecurity awareness purposes. All information is sourced from publicly available forensic research, court filings, and investigative journalism.*

*Last updated: June 2026*
