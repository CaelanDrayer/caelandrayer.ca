---
title: Intune Overview
draft: false
tags:
  - Intune
  - MDM
  - UEM
  - Policies
  - Endpoints
  - Security
  - Device
  - Management
  - Microsoft
aliases:
  - Intune Overview
description: An overview into Intune.
permalink: 
enableToc: true
cssclasses: 
created: 2025-04-13  15:01
modified: 2025-04-13  16:20
published: 
comments: true
---
# Preface
Intune is Microsoft's unified endpoint management solution. It has spent a few years maturing - and really provides a great feature set. Getting the full use of this feature set can be challenging - and some things are not as intuitive as one would like. I have been using Intune daily for almost 5 years; and have fleshed out some method and design considerations to getting things done. 

One of the key issues I have noted working with others in Intune - is the overall question of "Where do I do x / How do I do y?". I have built out a flowchart to send people in the right direction.  


> [!abstract]- Endpoint Security Blade versus Device Configuration
> I have made the choice to move settings that could be in a device configuration into the Endpoint Security blade. This is because of:
>
>1 - **Readability** - The GUI provided tends to be more human readable, and provide better tooltips
>
>2 - **Organization** - This approach standardizes locations amongst organizations; and prevent settings relating to a different function sneaking their way into what should be unrelated policies
> 
> 3 - **Microsoft** - Microsoft has been slowly but surely updating and improving this experience. Going with the flow seems appropriate here. 

> [!abstract]- Settings Catalog versus Templates
> I have made the choice to suggest that some things should be in a template versus a settings catalog policy. For some things this a requirement, for some things this is a preference.
> I tend to find that the templates are great when doing the basics. I would suggest take a look through the templates before using the settings catalog.
> 
> This does sometimes become an issue when the template is missing just one small setting you might expect. I would recommend not having things require two unique policies when it should be one. You will need to make a call on what is best for your purposes

> [!faq]-  This flowchart is a continual work in progress. Have something you aren't sure about how to achieve via Intune? Ask! I will happily add it in. 

> [!tip]- Welcome, to what is quite frankly a ridiculous flowchart. Happily - you can search it with CTRL-F.
> You can use Shift+MouseWheel or Num+/- to zoom 
> You when zoomed in; Shift-Click to pan


```mermaid
flowchart TD

start{"What are you trying to do?"} 

%% Applications
start -->|Deploy an application|app-q0{"App available on Microsoft Store(new)?"}
app-q0 -->|No|win32["Package as win32/intunewin & deploy as app"]

%% Printers
start -->|Push out Printers|print-q0{"Are you using a custom/3rd party print solution?"}
printers-q0 -->|Yes|printers-r0["Follow instructions from vendor"]
printers-q0 -->|No|printers-r1["Package driver & install script & deploy as app"] --> win32

%% Scripts
start -->|Run a script|script-q0{"Do you need to validate script success?"}
script-q0 -->|No|script-q1{"Does it need to run more than once?"}
script-q1 -->|No|platformscript["Platform Script"]
script-q0 & script-q1 -->|Yes|win32

%% Compliance
start -->|Monitor something|compliancenotif-q0{"What do you want to monitor?"}
compliancenotif-q0 -->|Simple Setting|compliancenotif-r0["Compliance Policy"]
compliancenotif-q0 -->|Custom Setting|compliancenotif-r1["Compliance Policy Script"]
compliancenotif-q0 -->|Other|compliancenotif-r2["Scheduled Task via Packaged Script"] --> win32

%% Config - Encryption
start -->|Change a setting|encryption-q0{"Relating to encryption?"}
encryption-q0 -->|Yes| encryption-r0["Endpoint Security/Disk Encyption"]

%% Config - AntiVirus
encryption-q0 -->|No|antivirus-q0{"Relating to AntiVirus?"}
antivirus-q0 -->|Yes| antivirus-r0["Endpoint Security/Antivirus"]

%% Config -  Firewall
antivirus-q0 -->|No|firewall-q0{"Relating to Windows Firewall?"}
firewall-q0 -->|Yes| firewall-r0["Endpoint Security/Firewall"]

%% Config -  edr
firewall-q0 -->|No|edr-q0{"Relating to MSFT EDR Enrollment?"}
firewall-q0 -->|Yes| edr-r0["Endpoint Security/Endpoint Detection & Response"]

%% Config -  ASR
firewall-q0 -->|No|asr-q0{"Relating to ASR?"}
asr-q0 -->|Yes| asr-r0["Endpoint Security/Attack Surface Reduction"]

%% Config -  LAPS
asr-q0 -->|No|laps-q0{"Relating to LAPS?"}
laps-q0 -->|Yes| laps-r0["Endpoint Security/Account Protection"]

%% Config -  Local Groups
laps-q0 -->|No|localgroups-q0{"Relating to Local Groups?"}
localgroups-q0 -->|Yes|localgroups-r0["Endpoint Security/Account Protection"]

%% Config -  Windows Hello
localgroups-q0 -->|No|windowsHello-q0{"Relating to Windows Hello?"}
windowsHello-q0 -->|Yes| windowsHello-r0["Endpoint Security/Account Protection"]

%% Config - BIOS
windowsHello-q0 -->|No|bios-q0{"Relating to a BIOS setting?"}
bios-q0 -->|Yes| bios-q1{"Is this for a Dell that supports .cctk files?"}
bios-q1 -->|Yes|bios-r1["Devices/Configuration<br>profile template<br>BIOS configurations and other settings"]
bios-q1 -->|no|bios-r2["Devices/Configuration<br>profile template<br>Device firmware configuration interface"]

%% Config - Domain
bios-q0 -->|No|domain-q0{"Joining a domain?<br>(NOT Entra ID)"}
domain-q0 -->|Yes| domain-r0["Devices/Configuration<br>profile template/domain"]

%% Config - certificates
domain-q0 -->|No|certs-q0{"Relating to Certificates?"}
certs-q0 -->|Yes|certs-q1{"Setting a trusted cert?"}
certs-q1 -->|Yes|certs-r0["Devices/Configuration<br>profile template/Trusted certificate"]
certs-q1 -->|no|certs-q2{"PKCS Cert?"}
certs-q2 -->|Yes|certs-r1["Devices/Configuration<br>profile template/PKCS certificate"]
certs-q2 -->|No|certs-q3{"SCEP Cert?"}
certs-q3 -->|Yes|certs-r2["Devices/Configuration<br>profile template/SCEP certificate"]

%% Config - wifi
certs-q3 -->|No|wifi-q0{"Relating to Wifi Passwords?"}
wifi-q0 -->|Yes|wifi-r0["Devices/Configuration<br>profile template/Wi-Fi"]

%% Config - vpn
wifi-q0 -->|No|vpn-q0{"Relating to built-in VPN?"}
vpn-q0 -->|Yes|vpn-r0["Devices/Configuration<br>profile template/VPN"]

%% Config - custom ADMX
vpn-q0 -->|No|customADMX-q0{"Relating to a custom ADMX?"}
customADMX-q0 -->|Yes|customADMX-r0["Devices/Configuration<br>profile template/Imported Administrative templates"]

%% Config -  Settings Catalog
customADMX-q0 -->|No|settingsCatalog-q0{"Is this setting in the Settings Catalog?"}
settingsCatalog-q0 -->|Yes|settingsCatalog-r0["Devices/Configurations/Settings catalog"]

%% Config -  Settings Catalog
settingsCatalog-q0 -->|No|registry-q0{"Is this a registry edit?"}
registry-q0 -->|Yes|registry-r0("Deploy as platform script")
registry-r0 -->script-q0
```


 
