# 👑 Golden Ticket Attack - CTF Challenge

## 📋 Overview

**Golden Ticket Attack** is an interactive, browser-based Capture The Flag (CTF) challenge designed for cybersecurity training. This challenge focuses on detecting Golden Ticket attacks where attackers forge Kerberos Ticket Granting Tickets (TGTs) using the compromised KRBTGT account hash. Participants learn to identify the absence of AS-REQ events (Event 4768), detect unusual logon patterns, and implement proper remediation procedures.

## 🎯 Learning Objectives

By completing this CTF, participants will learn:

- **Golden Ticket Recognition**: Identify indicators of forged Kerberos TGTs (missing Event 4768)
- **KRBTGT Compromise Detection**: Understand how KRBTGT hash compromise enables ticket forgery
- **Event Log Analysis**: Analyze Event 4624, 4768, and 4769 for anomalies
- **Incident Response**: Execute proper KRBTGT password reset procedures (twice)
- **Domain Recovery**: Understand when domain controller rebuilding is necessary
- **Attack Timeline Analysis**: Reconstruct Golden Ticket attack chronology

## 🛠️ Challenge Tasks (5 Total)

| Task | Description | Skill Focus |
|------|-------------|-------------|
| **Task 1** | Recognize Golden Ticket indicators (missing Event 4768) | Attack Recognition |
| **Task 2** | Identify KRBTGT hash compromise (LSASS dump) | Compromise Detection |
| **Task 3** | Reset KRBTGT password (must be done twice) | Incident Response |
| **Task 4** | Hunt for forged tickets via Event 4769 anomalies | Threat Hunting |
| **Task 5** | Determine need to rebuild domain controllers | Domain Recovery |

## 🚀 Quick Start

### Prerequisites
- A modern web browser (Chrome, Firefox, Edge, Safari)
- No server required - runs entirely in the browser
- No installation needed

### Access the Challenge
1. Open the HTML file directly in your browser
2. Enter your name
3. Use the password: `45_2026`
4. Complete all 5 tasks to capture the flag

### Hosting on GitHub Pages
1. Fork or clone this repository
2. Go to repository Settings > Pages
3. Select the branch (usually `main`) and save
4. Access via `https://your-username.github.io/repository-name`

## 🎮 How to Play

### Login
```
Password: 45_2026
Name: Enter any name (progress is saved locally)
```

### Game Features

- **Animated Attack Timeline**: Color-coded chronological events showing Golden Ticket attack progression
- **Security Event Logs**: Simulated Windows Event logs with highlighted suspicious entries
- **Attack Flow Visualization**: Step-by-step visual representation of the Golden Ticket attack
- **Indicator Analysis**: Missing Event 4768, unusual logon sources, privilege escalation
- **Remediation Guidance**: KRBTGT reset procedures and domain recovery steps
- **Answer Validation**: Immediate feedback on submitted answers
- **Progress Tracking**: Local storage saves your progress across sessions

### Completing Tasks
1. Read each task description carefully
2. Analyze the attack timeline and event logs
3. Identify the Golden Ticket indicators
4. Type your answer in the input field
5. Click "Submit" to validate
6. Complete all 5 tasks to reveal the flag

## 🏆 Flag

```
FLAG{GOLDEN_TICKET}
```

The flag is revealed only after completing all 5 tasks successfully.

## 📊 Challenge Details

### Attack Timeline

```
🔴 T0: Initial Compromise
   └── Attacker gains access via phishing

🟠 T+2h: LSASS Dump
   └── Mimikatz extracts KRBTGT hash from Domain Controller

🟡 T+3h: Golden Ticket Forged
   └── TGT created offline using stolen KRBTGT hash

🟣 T+3.5h: Suspicious Logons Detected
   └── Event 4624: Logon Type 3 from unusual workstation
   └── No corresponding Event 4768 (AS-REQ) found

🟢 T+4h: Detection
   └── SOC identifies Golden Ticket indicators
```

### Security Event Logs

```
❌ MISSING: Event 4768 (AS-REQ) - No TGT request found!
✅ Event 4624: Logon Type 3 | jsmith | 10.99.99.99 → DC01
✅ Event 4624: Logon Type 3 | jsmith | 10.99.99.99 → DC02
✅ Event 4672: Special Privileges assigned to jsmith
⚠️ Event 4769: TGS-REQ | jsmith → CIFS/DC01 (suspicious)
⚠️ Event 4769: TGS-REQ | jsmith → LDAP/DC01 (unusual SPN)
🔑 LSASS Dump detected 2 hours earlier on DC01
🔑 KRBTGT hash potentially compromised!
```

## 🔍 Investigation Walkthrough

### Task 1: Recognize Golden Ticket Indicators
The key indicator is the **missing Event 4768** (AS-REQ). In normal Kerberos authentication:
- Event 4768 records the TGT request (AS-REQ)
- Event 4769 records the service ticket request (TGS-REQ)
- Event 4624 records the successful logon

With Golden Ticket attacks:
- The TGT is forged offline using the KRBTGT hash
- No AS-REQ is sent to the Domain Controller
- Event 4768 is absent from logs
- Only Event 4624 appears with high privileges

### Task 2: KRBTGT Compromise
The **KRBTGT** account hash was compromised through an LSASS dump. This is critical because:
- KRBTGT is the most powerful account in Active Directory
- Its hash encrypts and signs all Kerberos tickets
- With this hash, attackers can forge TGTs for any user
- The forged tickets are indistinguishable from legitimate ones
- The compromise was detected 2 hours before the suspicious logons

### Task 3: Reset KRBTGT Password
The KRBTGT password must be reset **twice** because:
- First reset invalidates all existing tickets issued by old KRBTGT
- Second reset invalidates any tickets issued between resets
- Double reset ensures complete ticket invalidation
- Wait for replication between resets
- This is the only way to revoke Golden Tickets

### Task 4: Hunt Forged Tickets
**Event 4769** (TGS-REQ) should be analyzed for anomalies:
- TGS requests for unusual service principals (SPNs)
- Service tickets requested from unexpected sources
- Tickets with unusually long lifetimes
- Encryption type downgrade to RC4
- Multiple TGS requests in rapid succession

### Task 5: Rebuild Domain Controllers
**Domain controllers** must be rebuilt after a Golden Ticket attack because:
- The attacker had complete domain access
- Backdoors may have been installed
- System integrity cannot be guaranteed
- KRBTGT reset alone may not remove all persistence
- Clean rebuild ensures no attacker artifacts remain

## 🎨 Visual Features

- **Animated Attack Timeline**: Color-coded events with slide-in animations
- **Timeline Bar**: Vertical connecting line showing attack progression
- **Event Nodes**: Color-coded dots indicating different attack stages
- **Event Cards**: Detailed descriptions for each timeline event
- **Highlighted Logs**: Color-bordered log entries for different severity levels
- **Progress Indicators**: Visual completion status for each task
- **Glowing Flag Animation**: Celebratory golden flag reveal
- **Toast Notifications**: Non-intrusive success/error messages
- **Dark Theme**: Gold-accented UI for Kerberos/Golden Ticket theme

## 💾 Data Storage

- **Progress**: Saved in browser's `localStorage`
- **Persistence**: Progress survives page refreshes
- **Privacy**: All data stays on the user's device
- **Reset**: Clear browser data to reset progress

## 🛡️ Golden Ticket Detection Indicators

### High-Fidelity Indicators:
- Event 4624 (Logon) without corresponding Event 4768 (AS-REQ)
- Logon Type 3 from unexpected workstations
- TGT lifetime exceeds domain policy (default 10 hours)
- Special privileges (Event 4672) without normal authentication path
- Domain Admin logons from non-privileged workstations

### Medium-Fidelity Indicators:
- Multiple TGS requests in short time periods
- TGS requests for unusual service principals
- KRBTGT account last password change less than 24 hours
- LSASS access attempts on Domain Controllers

### Low-Fidelity Indicators:
- Single unusual logon event
- Event 4769 with RC4 encryption
- Logons during non-business hours

## 📁 File Structure

```
golden-ticket-attack/
│
├── index.html          # Main CTF challenge file
├── README.md           # This documentation
└── (no other files required)
```

## 🔧 Technical Implementation

- **Pure Frontend**: HTML5, CSS3, JavaScript (Vanilla)
- **No Dependencies**: Zero external libraries
- **Responsive Design**: Works on desktop and mobile
- **Animations**: CSS keyframe animations for timeline events
- **Storage**: Browser localStorage API
- **Gamification**: Progress tracking, badge system, visual rewards
- **Timeline Visualization**: Vertical timeline with animated event cards

## 📊 Golden Ticket Attack Flow

```
1. Initial Compromise
   └── Attacker gains access to Domain Controller

2. Credential Theft
   ├── Dump LSASS process memory
   ├── Extract KRBTGT NT hash
   └── Also extract domain SID

3. Ticket Forging (Offline)
   ├── Use KRBTGT hash to encrypt forged TGT
   ├── Set arbitrary user (e.g., Domain Admin)
   ├── Set long ticket lifetime
   └── Include high privileges in PAC

4. Lateral Movement
   ├── Present forged TGT to any domain resource
   ├── No AS-REQ needed (already have TGT)
   ├── Access any system as Domain Admin
   └── Persist indefinitely (KRBTGT rarely changes)

5. Detection Challenges
   ├── Forged tickets are cryptographically valid
   ├── No failed authentication logs
   ├── TGT appears legitimate
   └── Only missing AS-REQ reveals the attack
```

## 🔑 Kerberos Authentication Reference

### Normal Authentication Flow
```
1. AS-REQ (Event 4768) → DC returns TGT
2. TGS-REQ (Event 4769) → DC returns service ticket
3. AP-REQ → Service accepts ticket
4. Event 4624 logs successful logon
```

### Golden Ticket Flow
```
1. [NO AS-REQ] TGT forged offline with KRBTGT hash
2. TGS-REQ (Event 4769) → DC returns service ticket
3. AP-REQ → Service accepts forged ticket
4. Event 4624 logs successful logon
5. ⚠️ Missing Event 4768 is the detection indicator
```

## 🎓 Educational Use Cases

- **Cybersecurity Training Programs**
- **SOC Analyst Onboarding**
- **Active Directory Security Training**
- **Blue Team Exercises**
- **Incident Response Training**
- **Academic Courses** (Kerberos Security, AD Attacks)
- **Self-paced Learning**
- **Purple Team Exercises**

## 🔄 Version History

- **v1.0** - Initial release
  - 5 tasks with validation
  - Animated attack timeline with 5 stages
  - Simulated Event 4624, 4768, 4769 logs
  - Local storage progress tracking
  - Student login system

## 👥 Target Audience

- Security Operations Center (SOC) Analysts
- Incident Response Team Members
- Active Directory Security Specialists
- Threat Hunters
- Windows System Administrators
- Cybersecurity Students
- IT Security Professionals
- Blue Team Practitioners

---

**Happy Golden Ticket Hunting! 👑**
