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

```
---


# 1️⃣ المرحلة الأولى: بناء البيئة الرقمية (Build)
### Active Directory Domain Services
  
تم إعداد Windows Server وتثبيت خدمات النطاق (AD DS) وإنشاء Domain خاص بالبيئة تحت مسمى corp.local. كما تم إعداد Domain Controller وخدمات DNS لدعم بيئة Active Directory.

### اعدادات DNS Configuration
تم إعداد DNS وربطه بمتحكم النطاق لضمان قدرة أجهزة البيئة على الوصول إلى خدمات Active Directory وحل أسماء الدومين.
تم التحقق من دقة الاستعلام (DNS Resolution) باستخدام الأوامر التالية:

```
nslookup corp.local
```
```
Resolve-DnsName corp.local -Server 127.0.0.1
```
![DNS Configuration](screenshots/dns.config.png)
### اعدادات DHCP Configuration
تم إعداد DHCP لتوفير إعدادات الشبكة (IP, Subnet, Gateway) للأجهزة العميلة داخل بيئة الـ Lab تلقائياً.
![DHCP Configuration](screenshots/dhcp.config.png)
* تأسيس Active Directory والهيكل التنظيمي (AD Forest & OUs)
* تنفيذ أمر تثبيت Forest: تم تشغيل أوامر PowerShell لإنشاء النطاق corp.local مع تعيين كلمات المرور الآمنة.
* ستعراض الهيكل التنظيمي 
تأكيد بناء الهيكل التنظيمي (OUs) وتوزيع الأقسام الرئيسية (HR, Finance, IT, Management).
* إعدادات الشبكة: تم تنفيذ أمر ipconfig /all للتحقق من اسم المضيف (DC01)، ونطاق العمل، وعنوان الـ IP الثابت وتوجيه خادم الـ DNS المحلي.
* إدارة المستخدمين والحواسيب (ADUC): استعراض الواجهة الرسومية وتوزيع الوحدات التنظيمية، مع استخدام الأمر التالي للاستعلام عنها:

```
Get-ADOrganizationalUnit -Filter * | Select Name, DistinguishedName
```
![Ous ](screenshots/ous.png)


---
# 2️⃣ الهيكل التنظيمي للوحدات (Organizational Units — OUs)
تم تصميم هيكل تنظيمي يحاكي بيئة مؤسسة حقيقية، مع فصل المستخدمين والأجهزة والحسابات حسب الوظيفة كالتالي:
```
corp.local
│
├── User-Accounts
│   ├── HR
│   ├── Finance
│   ├── IT
│   └── Management
│
├── Computer-Devices
│   ├── Workstations
│   └── Servers
│
├── Groups
│
├── Service Accounts
│
└── Domain Controllers
```
---
# 3️⃣ إدارة الحسابات والمستخدمين (User & Account Management)
تم إنشاء حسابات المستخدمين وتوزيعها على الـ OUs المناسبة حسب الأقسام الوظيفية.
📌 عينة من الحسابات (Sample Accounts):

- Ali Ahmed
- Sara HR
- Omar Finance
- Mona Manager
- Lina Security
- IT Admin
---
# 4️⃣ المجموعات الأمنية (Security Groups & RBAC)
تم إنشاء مجموعات أمنية عالمية (Global Security Groups) لتطبيق مبدأ التحكم في الوصول المستند إلى الدور (Role-Based Access Control - RBAC):

- GG-IT-Users
- GG-HR-Users
- GG-Finance-Users
- GG-Management-Users
- GG-IT-Admins
- GG-Security-Analysts
---
# 5️⃣ تنظيم الأجهزة والخوادم (Computer & Server Organization)
تم إنشاء هيكل منفصل لإدارة أجهزة المؤسسة لتسهيل تطبيق السياسات (GPOs) المخصصة لكل نوع:
```
Computer-Devices
│
├── Workstations
└── Servers
```
---
# 6️⃣ الانضمام للنطاق (Domain Join)
تم ربط جهاز العميل PC01 بالنطاق corp.local ليصبح جزءًا من بيئة Active Directory ويستفيد من سياسات الدومين والمصادقة المركزية.
---
# 7️⃣ إدارة الأنظمة (Administration)
* 🔐 إدارة الحسابات والصلاحيات (Account & Permission Management)
تم تطبيق مبادئ أمنية قياسية تشمل:Role-Based Access Control (RBAC).
مبدأ الصلاحيات الأقل (Least Privilege).
إدارة الحسابات الإدارية وحسابات الخدمات (Service Accounts).

* 📁 مشاركة الملفات (File Sharing & NTFS Permissions)
تم إعداد موارد مشاركة الملفات وتطبيق الصلاحيات بدقة:

NTFS Permissions
SMB Permissions
Shared Folders Access Control
---
# 8️⃣ التصليد الأمني (Security Hardening)
تطبيق مجموعة من إجراءات تقليل سطح الهجوم وتحسين الوضع الأمني.

* 🔐 سياسات قفل الحسابات وكلمات المرور:

تطبيق تعقيد كلمات المرور (Password Complexity).

سياسة الحظر التلقائي (Account Lockout Policy) للحد من هجمات التخمين.

* الحماية والجدار الناري:

تفعيل Windows Defender.
ضبط إعدادات وقواعد Windows Firewall.

* التدقيق المتقدم (Advanced Auditing):

تفعيل سياسات التدقيق المتقدمة لتسجيل الأنشطة الأمنية الحساسة.
---
# 9️⃣ المراقبة الطرفية (Sysmon — Endpoint Monitoring)
تم استخدام أداة Sysmon لمراقبة الأجهزة وتوفير معلومات تفصيلية عن العمليات والأنشطة الشبكية.

أهم الأحداث المرصودة: Sysmon Event ID 1 (Process Creation).
---
#🔟 إدارة السجلات الأمنية (Splunk SIEM)
تم بناء مسار مركزي لجمع وتحليل السجلات بشكل فوري:
```
[ Windows Event Logs + Sysmon ]
             │
             ▼
[ Splunk Universal Forwarder ]
             │
             ▼
[ Splunk Enterprise ] (Detection & Investigation)
```
---
### استعلامات الرصد (SPL Queries)
🚨 رصد هجوم التخمين (Brute Force Detection):
يستخدم لاكتشاف المصادر التي تجاوزت حدًا معينًا من محاولات الدخول الفاشلة.
```
index=* EventCode=4625 
| stats count by Account_Name, Workstation_Name, Source_Network_Address 
| where count >= 5
```
---
### التحقيق الجنائي الرقمي (Forensic Investigation):
يستخدم لاستخراج المعلومات الأساسية اللازمة للتحقيق.
```
index=* EventCode=4625 
| table _time, Account_Name, Workstation_Name, Source_Network_Address, Source_Port

```
### الاستجابة للحوادث: سيناريو هجوم (SMB Brute Force Attack)
تم تنفيذ محاكاة عملية لهجوم تخمين على خدمة SMB.

⚔️ سيناريو الهجوم (Attack Flow):
```
[ Attacker: Kali Linux ] 
        │ (Hydra SMB Brute Force)
        ▼
[ Target: PC01 ] 
        │ (Failed Authentication)
        ▼
[ Windows Security Event 4625 ] 
        │
        ▼
[ Splunk SIEM Detection ] 
        │ (Investigation via SPL)
        ▼
[ MITRE ATT&CK T1110 ] 
        │
        ▼
[ Containment & Remediation ]
```
### تفاصيل ومؤشرات الهجوم (Attack Details & IoCs)
الهدف (Target): PC01 (192.168.10.20)

نظام المهاجم (Attacker): Kali Linux (192.168.10.50)

البروتوكول المستخدم: SMB

نوع الهجوم: Brute Force

الحدث المرصود (Event): 4625 - Failed Logon

### 🗺️ تصنيف الهجوم (MITRE ATT&CK Mapping)
تم تصنيف الهجوم تحت إطار MITRE:

التكتيك: Credential Access

التقنية: T1110 — Brute Force

### 🛡️ الاحتواء والمعالجة (Containment & Remediation)
تم التعامل مع الحادثة وفق دورة الاستجابة للحوادث:
Detection ➔ Investigation ➔ Identification ➔ Containment ➔ Remediation

الاعتماد الأساسي كان على Account Lockout Policy لإيقاف التخمين آلياً والحد من فعالية الهجوم.

### الأدوات والتقنيات المستخدمة (Tools & Technologies)
Windows Server & Client

Active Directory, DNS, DHCP, Group Policy

PowerShell

Windows Defender & Firewall

Sysmon

Splunk Enterprise & Universal Forwarder

Kali Linux & Hydra

MITRE ATT&CK Framework

### المهارات المكتسبة والمطبقة (Skills Demonstrated)
إدارة خوادم Windows و Active Directory.

تصميم الهياكل التنظيمية (OUs) وإدارة الصلاحيات (RBAC).

تطبيق سياسات التصليد الأمني (Security Hardening).

المراقبة وتدقيق السجلات (Security Auditing & Sysmon).

بناء واستخدام أنظمة SIEM (Splunk SPL).

رصد التهديدات والتحقيق الجنائي الرقمي (Threat Detection & Digital Investigation).

الاستجابة للحوادث وربط التهديدات بإطار (MITRE ATT&CK).

### خاتمة المشروع (Project Outcome)
تم بناء بيئة Windows Enterprise متكاملة تبدأ من إنشاء Domain Controller وActive Directory، مرورًا بإدارة المستخدمين والأجهزة وتطبيق إجراءات التصليد، وانتهاءً ببناء منظومة مراقبة متكاملة باستخدام Sysmon و Splunk SIEM.

كما نجح المعمل في تنفيذ وتوثيق سيناريو هجوم اختراق (SMB Brute Force)، واكتشافه وتحليله باستخدام السجلات الأمنية، مع تفعيل إجراءات الاستجابة والاحتواء القياسية. يبرهن هذا المشروع على كفاءة تطبيق مهارات إدارة الأنظمة والأمن السيبراني في بيئة مؤسسية حقيقية.

