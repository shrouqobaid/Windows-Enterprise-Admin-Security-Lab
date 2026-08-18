# 🛡️ Windows Enterprise Administration & Security Lab

### بيئة متكاملة لإدارة وتأمين أنظمة Windows ورصد التهديدات

---

## 📌 Project Overview

يقدم هذا المشروع تطبيقًا عمليًا متكاملًا لبناء وإدارة وتأمين بيئة **Windows Enterprise**، بدءًا من إنشاء **Active Directory Domain Services (AD DS)** وتنظيم المستخدمين والأجهزة والمجموعات، مرورًا بتطبيق إجراءات **Security Hardening**، ووصولًا إلى جمع وتحليل السجلات الأمنية باستخدام **Sysmon** و **Splunk SIEM**.

يتضمن المشروع أيضًا محاكاة لهجوم **SMB Brute Force** باستخدام Kali Linux، واكتشاف الهجوم وتحليله من خلال Windows Security Logs وSplunk، مع ربط السيناريو بإطار **MITRE ATT&CK** وتطبيق إجراءات الاستجابة للحوادث.

---

# 🏗️ Infrastructure & Lab Environment

| Component | Description |
|---|---|
| **DC01** | Windows Server – Active Directory, DNS, DHCP |
| **PC01** | Windows Client – Domain Joined |
| **Splunk Enterprise** | SIEM & Log Analysis |
| **Splunk Universal Forwarder** | Centralized Log Collection |
| **Sysmon** | Endpoint Monitoring |
| **Kali Linux** | Attack Simulation |
| **Domain** | `corp.local` |
| **Network** | `192.168.10.0/24` |

### 🌐 Network Configuration

```text
DC01        → 192.168.10.10
PC01        → 192.168.10.20
Kali Linux  → 192.168.10.50
Domain      → corp.local
Network     → 192.168.10.0/24
