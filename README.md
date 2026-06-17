# 🛡️ Home Lab — Cybersecurity (Blue Team)

Laboratorio virtuale su VirtualBox per simulare attacchi reali e analizzare il rilevamento tramite SIEM.

## 🖧 Topologia di rete

| VM | IP | Ruolo |
|---|---|---|
| pfSense | 10.0.3.1 | Firewall/Router |
| Windows Server 2022 | 10.0.3.13 | Active Directory (lab.local) |
| Wazuh SIEM | 10.0.3.12 | SIEM / Log Analysis |
| Kali Linux | 10.0.3.11 | Attaccante simulato |

Rete: `10.0.3.0/24` — LAN virtuale sotto pfSense

## 🔧 Stack tecnologico

- **Firewall:** pfSense
- **SIEM:** Wazuh + Sysmon
- **AD:** Windows Server 2022 (dominio `lab.local`)
- **Attaccante:** Kali Linux
- **Vulnerability Scanner:** Nessus
- **AD Enumeration:** BloodHound + SharpHound

## ⚔️ Attacchi simulati e rilevamento

### 🔴 Brute Force (Event ID 4625)
- Attacco con Hydra su RDP/SMB
- Wazuh alert: regola custom per 5+ fallimenti in 30 secondi
- Event ID: `4625` (logon failure)

### 🔴 Kerberoasting
- Tool: Impacket `GetUserSPNs.py`
- Account target: `sqlsvc` (SPN: `MSSQLSvc/lab.local:1433`)
- Hash crackato con John the Ripper + rockyou.txt
- Password trovata: `Password123`
- Wazuh alert: regola custom ID `100001` per Event ID `4769` RC4

### 🔴 AD Enumeration — BloodHound
- SharpHound collector eseguito su Windows Server
- Grafi di attacco visualizzati in BloodHound
- Identificati path verso Domain Admin

### 🔴 Port Scan (Nmap)
- Nmap da Kali verso WinServer
- Windows Firewall blocca traffico da `10.0.3.11`
- 1000 porte filtered — blocco confermato

### 🔴 Vulnerability Scan
- Nessus scan su Windows Server
- Identificate vulnerabilità critiche e missing patch

## 🔵 Difesa e rilevamento

- Regole Wazuh custom per Kerberoasting (Event ID 4769 RC4)
- Regola Wazuh per brute force (Event ID 4625)
- Sysmon configurato con regole per process creation e network connections
- Windows Firewall: blocco IP Kali (10.0.3.11)
- pfSense: segmentazione rete e logging traffico

## 📊 Risultati Wazuh (esempio)

- 10.000+ eventi in 24h
- 74 alert critici (livello 12+)
- 4.367 authentication failures rilevate
- Mappatura MITRE ATT&CK: T1110 (Brute Force), T1558.003 (Kerberoasting)

## 🎯 Prossimi step

- [ ] Mimikatz Pass-the-Hash
- [ ] Golden Ticket attack
- [ ] DCSync
- [ ] Suricata IDS integrato con Wazuh
- [ ] LLMNR/NBT-NS Poisoning (Responder)
