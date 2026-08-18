🛡️ Windows Enterprise Administration & Security Lab

بيئة متكاملة لإدارة وتأمين أنظمة Windows ورصد التهديدات

⸻

📌 Project Overview

يقدم هذا المشروع تطبيقًا عمليًا متكاملًا لبناء وإدارة وتأمين بيئة Windows Enterprise، بدءًا من إنشاء Active Directory Domain Services (AD DS) وتنظيم المستخدمين والأجهزة والمجموعات، مرورًا بتطبيق إجراءات Security Hardening، ووصولًا إلى جمع وتحليل السجلات الأمنية باستخدام Sysmon و Splunk SIEM.

يتضمن المشروع أيضًا محاكاة لهجوم SMB Brute Force باستخدام Kali Linux، واكتشاف الهجوم وتحليله من خلال Windows Security Logs وSplunk، مع ربط السيناريو بإطار MITRE ATT&CK وتطبيق إجراءات الاستجابة للحوادث.

⸻

🏗️ Infrastructure & Lab Environment

Component	Description
DC01	Windows Server – Active Directory, DNS, DHCP
PC01	Windows Client – Domain Joined
Splunk Enterprise	SIEM & Log Analysis
Splunk Universal Forwarder	Centralized Log Collection
Sysmon	Endpoint Monitoring
Kali Linux	Attack Simulation
Domain	corp.local
Network	192.168.10.0/24

🌐 Network Configuration

DC01        → 192.168.10.10
PC01        → 192.168.10.20
Kali Linux  → 192.168.10.50
Domain      → corp.local
Network     → 192.168.10.0/24

⸻

1️⃣ Build — بناء البيئة الرقمية

Active Directory Domain Services

تم إعداد Windows Server وتثبيت Active Directory Domain Services (AD DS) وإنشاء Domain خاص بالبيئة:

corp.local

كما تم إعداد Domain Controller وخدمات DNS لدعم بيئة Active Directory.

DNS Configuration

تم إعداد DNS وربطه بالـ Domain Controller لضمان قدرة أجهزة البيئة على الوصول إلى خدمات Active Directory وحل أسماء الدومين.

تم التحقق من DNS Resolution باستخدام:

nslookup corp.local

والتحقق من DNS باستخدام:

Resolve-DnsName corp.local -Server 127.0.0.1

DHCP Configuration

تم إعداد DHCP لتوفير إعدادات الشبكة للأجهزة العميلة داخل بيئة الـ Lab.
---

###  تأسيس Active Directory والهيكل التنظيمي (AD Forest & OUs Structure)

#### أ. تنفيذ أمر تثبيت Forest وإنشاء نطاق العمل (Active Directory Forest Installation)
تظهر الصورة عملية تشغيل أمر `Install-ADDSForest` وإنشاء النطاق `corp.local` مع التحقق من البيئة وتعيين كلمات المرور الآمنة.
![AD Forest Installation](screenshots/Screenshot_2026-08-17_151727_2.png)

#### ب. استعراض الهيكل التنظيمي للوحدات (Organizational Units Structure)
تأكيد بناء الهيكل التنظيمي (OUs) وتوزيع الأقسام الرئيسية (`HR`, `Finance`, `IT`, `Management`) ووحدات أجهزة الحاسوب والمستخدمين باستخدام PowerShell.
![AD Organizational Units](screenshots/Screenshot_2026-08-17_214321_2.png)
---

###  إعدادات الشبكة وإدارة النطاق (Network Configuration & ADUC)

#### أ. التحقق من إعدادات شبكة متحكم النطاق (IP Configuration)
توضح الصورة نتيجة تنفيذ أمر `ipconfig /all` للتحقق من اسم المضيف (`DC01`)، ونطاق العمل (`corp.local`)، وعنوان الـ IP الثابت (`192.168.10.10`) وتوجيه خادم الـ DNS المحلي.
![IP Config Details](screenshots/Screenshot_2026-08-17_214525_2.jpg)

#### ب. استعراض واجهة إدارة المستخدمين والحواسيب (Active Directory Users and Computers - ADUC)
عرض واجهة الإدارة الرسومية وتوزيع الوحدات التنظيمية الخاصة بحسابات المستخدمين (`User-Accounts`) مثل أقسام (`Finance`, `HR`, `IT`, `Management`).
![ADUC Management Interface](screenshots/Screenshot_2026-08-17_215342_2.png)
---
*الأمر المستخدم للاستعلام عن الوحدات التنظيمية:*
```powershell
Get-ADOrganizationalUnit -Filter * | Select Name, DistinguishedName
⸻

2️⃣ Organizational Units — OUs

تم تصميم هيكل تنظيمي يحاكي بيئة مؤسسة حقيقية، مع فصل المستخدمين والأجهزة والحسابات حسب الوظيفة والاستخدام:

corp.local
│
├── User-Accounts
│   ├── HR
│   ├── Finance
│   ├── IT
│   └── Management
│
├── Computer-Devices
│   ├── Workstations
│   └── Servers
│
├── Groups
│
├── Service Accounts
│
└── Domain Controllers

تم إنشاء الـ OUs وإدارتها باستخدام PowerShell Active Directory Module.

⸻

3️⃣ User & Account Management

تم إنشاء حسابات المستخدمين وتوزيعها على الـ OUs المناسبة حسب الأقسام الوظيفية.

Sample Accounts

* Ali Ahmed
* Sara HR
* Omar Finance
* Mona Manager
* Lina Security
* IT Admin

⸻

4️⃣ Security Groups & RBAC

تم إنشاء Global Security Groups لتطبيق مبدأ:

Role-Based Access Control (RBAC)

Security Groups

* GG-IT-Users
* GG-HR-Users
* GG-Finance-Users
* GG-Management-Users
* GG-IT-Admins
* GG-Security-Analysts

⸻

5️⃣ Computer & Server Organization

تم إنشاء هيكل منفصل لإدارة أجهزة المؤسسة:

Computer-Devices
│
├── Workstations
└── Servers

يساعد هذا التقسيم على فصل أجهزة المستخدمين عن الخوادم وتطبيق السياسات المناسبة لكل نوع من الأجهزة.

⸻

6️⃣ Domain Join

تم ربط PC01 بالـ Domain:

corp.local

ليصبح الجهاز جزءًا من بيئة Active Directory ويتمكن من الاستفادة من سياسات الدومين والمصادقة المركزية.

⸻

7️⃣ Administration — إدارة الأنظمة

Account & Permission Management

تم تطبيق مبادئ إدارة الحسابات والصلاحيات، مع التركيز على:

* Role-Based Access Control
* Least Privilege
* User & Group Management
* Administrative Accounts
* Service Accounts

File Sharing & NTFS Permissions

تم إعداد موارد مشاركة الملفات وتطبيق صلاحيات:

* NTFS Permissions
* SMB Permissions
* Shared Folders
* Access Control

⸻

8️⃣ Security Hardening

تم تطبيق مجموعة من إجراءات Windows Security Hardening لتقليل سطح الهجوم وتحسين الوضع الأمني للبيئة.

🔐 Password & Account Lockout Policies

تم تطبيق:

* Password Complexity
* Password Policy
* Account Lockout Policy
* الحد من محاولات تسجيل الدخول الفاشلة
* Least Privilege

🛡️ Windows Defender & Firewall

تم تفعيل ومراجعة:

* Windows Defender
* Windows Firewall
* Firewall Rules
* Security Settings

🔎 Advanced Auditing

تم تفعيل ومراجعة Advanced Audit Policies لتسجيل الأنشطة الأمنية المهمة داخل النظام.

⸻

9️⃣ Sysmon — Endpoint Monitoring

تم استخدام Sysmon (System Monitor) لمراقبة الأنشطة المهمة على أجهزة Windows وتوفير معلومات تفصيلية عن العمليات والأنشطة الشبكية.

من أهم الأحداث التي تمت مراقبتها:

Sysmon Event ID 1

Process Creation

بالإضافة إلى أحداث أخرى مرتبطة بالنشاط الشبكي وتنفيذ الأوامر.

⸻

🔟 Splunk SIEM

تم بناء مسار مركزي لجمع وتحليل السجلات:

Windows Event Logs + Sysmon
            │
            ▼
Splunk Universal Forwarder
            │
            ▼
Splunk Enterprise
            │
            ▼
Detection & Investigation

تم استخدام Splunk كمنصة SIEM لمراقبة الأحداث الأمنية وتحليل الأنشطة المشبوهة.

📊 SPL Queries & Detection

تم كتابة استعلامات SPL مخصصة لاكتشاف وتحليل الأنشطة الأمنية.

🚨 Brute Force Detection

index=* EventCode=4625
| stats count by Account_Name, Workstation_Name, Source_Network_Address
| where count >= 5

يستخدم الاستعلام لتجميع محاولات تسجيل الدخول الفاشلة واكتشاف المصادر التي تجاوزت حدًا معينًا من المحاولات.

🕵️ Forensic Investigation

index=* EventCode=4625
| table _time, Account_Name, Workstation_Name, Source_Network_Address, Source_Port

يستخدم هذا الاستعلام لاستخراج المعلومات الأساسية اللازمة للتحقيق في محاولات تسجيل الدخول الفاشلة.

⸻

🚨 Incident Response — Brute Force Attack

تم تنفيذ سيناريو محاكاة لهجوم SMB Brute Force باستخدام Kali Linux وأداة Hydra.

Attack Scenario

[ Attacker: Kali Linux ]
        │
        │ Hydra SMB Brute Force
        ▼
[ Target: PC01 ]
        │
        │ Failed Authentication
        ▼
[ Windows Security Event 4625 ]
        │
        ▼
[ Splunk SIEM ]
        │
        │ Detection
        ▼
[ Investigation via SPL ]
        │
        ▼
[ MITRE ATT&CK T1110 ]
        │
        ▼
[ Containment & Remediation ]

Attack Details

Attacker:

Kali Linux
192.168.10.50

Target:

PC01
192.168.10.20

Protocol:

SMB

Attack:

Brute Force

⸻

🔍 Detection

تم اكتشاف الهجوم من خلال تكرار محاولات تسجيل الدخول الفاشلة:

Event ID 4625 — Failed Logon

ثم تم إرسال الأحداث إلى Splunk وتحليلها لاكتشاف نمط Brute Force.

⸻

🕵️ Investigation

تم تحليل السجلات لتحديد:

* الحساب المستهدف
* عنوان IP للمهاجم
* اسم الجهاز المصدر
* عدد المحاولات
* وقت الهجوم
* Source Port
* مصدر النشاط

Identified Indicators

Target:
PC01

Attacker IP:
192.168.10.50

Attacker:
KALI

Event:
4625 - Failed Logon

⸻

🎯 MITRE ATT&CK Mapping

تم تصنيف الهجوم تحت:

T1110 — Brute Force

ضمن تكتيك Credential Access.

⸻

🔐 Containment & Remediation

تم التعامل مع الحادثة وفق مراحل Incident Response:

Detection
   ↓
Investigation
   ↓
Identification
   ↓
Containment
   ↓
Remediation

تم الاعتماد على Account Lockout Policy للحد من محاولات التخمين المتكررة، بالإضافة إلى تحديد مصدر النشاط المشبوه واتخاذ إجراءات الاحتواء المناسبة.

⸻

🧰 Tools & Technologies

* Windows Server
* Windows Client
* Active Directory
* DNS
* DHCP
* Group Policy
* PowerShell
* Windows Defender
* Windows Firewall
* Sysmon
* Splunk Enterprise
* Splunk Universal Forwarder
* Kali Linux
* Hydra
* SMB
* MITRE ATT&CK

⸻

🎯 Skills Demonstrated

من خلال تنفيذ المشروع تم تطبيق مهارات عملية في:

* Active Directory Administration
* Windows Server Administration
* User & Group Management
* Organizational Unit Design
* RBAC
* DNS & DHCP
* Windows Security Hardening
* Group Policy
* Security Auditing
* Sysmon Monitoring
* SIEM Deployment
* Splunk SPL
* Threat Detection
* Log Analysis
* Brute Force Detection
* Digital Investigation
* MITRE ATT&CK Mapping
* Incident Response

⸻

📁 Project Structure

🏁 Project Outcome

تم بناء بيئة Windows Enterprise متكاملة تبدأ من إنشاء Domain Controller وActive Directory، مرورًا بإدارة المستخدمين والأجهزة والمجموعات وتطبيق إجراءات الـHardening، وانتهاءً ببناء منظومة مراقبة باستخدام Sysmon + Splunk SIEM.

كما تم تنفيذ سيناريو SMB Brute Force واكتشافه وتحليله باستخدام Windows Event Logs وSplunk، وربطه بإطار MITRE ATT&CK وتطبيق إجراءات الاستجابة والاحتواء.

This project demonstrates practical skills in Windows Enterprise Administration, Security Hardening, SIEM Monitoring, Threat Detection, and Incident Response.

⸻

📸 Screenshots

جميع لقطات المشروع متوفرة داخل
