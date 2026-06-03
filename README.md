<p align="center">
  <h1 align="center">🛡️ SOC Analyst Homelab</h1>
  <p align="center">A detection engineering homelab simulating real-world adversary techniques using Wazuh, MITRE Caldera, Sysmon, and AI-assisted log analysis.</p>
</p>
<p align="center">
  <img src="https://img.shields.io/badge/Wazuh-4.x-blue?style=flat-square&logo=wazuh" />
  <img src="https://img.shields.io/badge/MITRE%20Caldera-5.x-red?style=flat-square" />
  <img src="https://img.shields.io/badge/Sysmon-15.x-orange?style=flat-square" />
  <img src="https://img.shields.io/badge/AI%20Powered-LLM%20Triage-purple?style=flat-square" />
  <img src="https://img.shields.io/github/last-commit/youruser/soc-homelab?style=flat-square" />
</p>




**2. Overview**
___

This homelab is a fully self-contained, virtualized Security Operations Center (SOC) environment to simulate cyberattacks, monitor security events, and analyze logs using AI-assisted log analysis.

**Related Objectives:**

Threat Detection — Build, tune, and trigger custom Wazuh SIEM correlation rules mapped to the MITRE ATT&CK framework

Adversary Simulation — Use MITRE Caldera to emulate threat actor TTPs (Tactics, Techniques, and Procedures) in a controlled environment

Endpoint Telemetry — Capture granular Windows process, network, and file-system events via Sysmon and forward them to Wazuh

AI-Assisted Triage — Feed raw Wazuh alerts into an LLM to generate natural-language summaries and surface high-priority findings faster


**3. Architecture**
___

```
┌─────────────────────────────────────────────────────────────┐
│                     Host Machine                            │
│                                                             │
│  ┌──────────────────┐        ┌──────────────────────────┐   │
│  │  Attacker VM     │        │   Victim VM (Windows 10) │   │
│  │  MITRE Caldera   │◄──C2──►│   Wazuh Agent            │   │
│  │  (Kali Linux)    |        │   Sysmon                 │   │
│  └──────────────────┘        └──────────┬───────────────┘   │
│                                         │ Syslog / Beats    │
│  ┌──────────────────────────────────────▼───────────────┐   │
│  │              Wazuh Manager (Ubuntu)                  │   │
│  │          │ Dashboard │ API | Indexer |               │   │
│  └──────────────────────────────────────┬───────────────┘   │
│                                         │ Log Analysis      │
│  ┌──────────────────────────────────────▼───────────────┐   │
│  │           AI Analysis Layer (Python)                 │   │
│  │   Polls alerts → formats prompt → calls Ollama       │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

Virtualization Platform:

This lab was built on VMware, but the configuration is compatible with with minor networking adjustments.

**4. Tech Stack**
___
```
Component     Version    Purpose  

Wazuh          4.x       SIEM / XDR — log ingestion, alerting, dashboards
MITRE Caldera  5.x       Adversary emulation C2 framework
Sysmon         15.x      Windows endpoint telemetry
Kali Linux     12.x      MITRE Caldera host
Python         3.11+     AI analysis glue scripts
Ollama         latest    LLM backend for alert triaging
Ubuntu Server  22.04LTS  Wazuh Manager
Windows 10     22H2      Victim/target endpoint
```


**5. Prerequisites**
___

**Software:**
```
Ubuntu Server (Wazuh Server)

Windows 10 VM (Target Computer)

Kali Linux (Attacker Machine)

```
**6. Installation & Setup**

The section provided shows the steps done to complete the project.

_6.1 Step 1:_ Create and Load the Virtual Machines in VMware

<img width="1920" height="1080" alt="Screenshot (1)" src="https://github.com/user-attachments/assets/1da009f8-4a4c-4731-acd7-211a76b41e90" />

_6.2 Step 2:_ Install and Configure Wazuh

<img width="1920" height="1080" alt="Screenshot (2)" src="https://github.com/user-attachments/assets/3262885f-3796-4fad-8bef-8d4e7e15b818" />

After installing the Ubuntu Server VM and configured the Wazuh Manager, Indexer, and Dashboard. After install, verify all services were running and confirmed access to the web dashboard.

<img width="1920" height="1080" alt="Screenshot (3)" src="https://github.com/user-attachments/assets/cde68a55-9059-45c4-8a9e-673a96ca4939" />

After the setup, your dashboard should appear as so.

_6.3 Step 3:_ Add the Windows 10 Endpoint

After installing Windows 10, register it with the Wazuh Agent. 

<img width="1920" height="1080" alt="Screenshot (5)" src="https://github.com/user-attachments/assets/3a99efc4-bd0b-4243-ae04-c0b67bcfb647" />

Check for the proper IP using ``ip a`` command in Ubuntu to display working IP's. The proper IP is next to the second ``inet`` starting with ``192..`` and ending with ``12..``.

<img width="1920" height="1080" alt="Screenshot (6)" src="https://github.com/user-attachments/assets/ae44d944-8d65-49cf-b8c1-d5eccb2afb4d" />

If connected, it should like so

<img width="1920" height="1080" alt="Screenshot (7)" src="https://github.com/user-attachments/assets/e4092933-b7fd-49d3-8c7e-a9ecb711b40d" />

_6.4 Step 4: Install and Configure Sysmon_ 

After installing Sysmon64.exe, configure with these commands. 

<img width="1920" height="1080" alt="Screenshot (8)" src="https://github.com/user-attachments/assets/9418923a-c284-43a5-8923-3185e9ca1e9d" />

Check if Sysmon is runing operationally. 

<img width="1920" height="1080" alt="Screenshot (9)" src="https://github.com/user-attachments/assets/40ec9d56-baa2-4c04-a7b8-73755270807f" />

_6.5 Step 5: Install Kali Linux_ 

After setting up Kali Linux, install and setup MITRE Caldera to simulate the adversary using attacks based on MITRE ATT&CK framework.

<img width="1920" height="1080" alt="Screenshot (10)" src="https://github.com/user-attachments/assets/e2d2c873-fa48-43b1-b9ef-12c815fb5ee8" />

Use these commands to begin the Caldera server operation.

<img width="1920" height="1080" alt="Screenshot (11)" src="https://github.com/user-attachments/assets/b603896a-31d6-442c-ba11-f442e51430eb" />

It should display 'CALDERA' once successfully booted.

<img width="1920" height="1080" alt="Screenshot (12)" src="https://github.com/user-attachments/assets/f1e7a249-98ed-42f9-b518-8580149f85f9" />

After logging in with the ``admin`` username and ``red`` password under the ``http://localhost:8888`` URL, the dashboard should be visible.

_6.6 Step 6: Deploy the Windows Agent for Caldera_ 

Select ``Deploy Agent`` in Magma Caldera and select ``Sandcat``. From there copy and paste the code into the Windows VM.

<img width="1920" height="1080" alt="Screenshot (13)" src="https://github.com/user-attachments/assets/69e7d2e4-9be7-49e0-84b9-db74e27d9811" />

Once done, it should look like so.

<img width="1920" height="1080" alt="Screenshot (14)" src="https://github.com/user-attachments/assets/9b10679e-fe9c-46ef-8e61-630a9853aa12" />

_6.7 Step 7: Execute Attack Simluations_

Begin with going to ``Operations``, from there I have done three operations; Enumeration, Thief, and Worm. In this case, I used Enumeration.

<img width="1920" height="1080" alt="Screenshot (15)" src="https://github.com/user-attachments/assets/325b7709-b5c3-4b4d-bf18-deca47ca7d5e" />

_6.8 Step 8: Monitor and Investigate Alerts in Wazuh_

Back to Ubunutu, check the ``MITRE ATT&CK`` section and select ``Events`` to see all the current processes.

<img width="1920" height="1080" alt="Screenshot (16)" src="https://github.com/user-attachments/assets/a43a2333-aab4-4d4c-b7fa-1d809ffeac6f" />

Since Enumeration is 'gathering inlliegence' operation, it is classified with a ``T1078 MITRE ID``.

<img width="1920" height="1080" alt="Screenshot (17)" src="https://github.com/user-attachments/assets/7391791c-970c-4af2-81ab-df95cc69a2f8" />

Further event investigation. 

_6.9 Step 9: Install Ollama_ 

Installation of Ollama.

<img width="1920" height="1080" alt="Screenshot (18)" src="https://github.com/user-attachments/assets/8eabea84-92f5-40d8-9a25-6fa780ccfb0c" />

Then, run the LLM tailored to triaging logs.

<img width="1920" height="1080" alt="Screenshot (19)" src="https://github.com/user-attachments/assets/803c20e7-6a9d-4d70-b08d-39c543d5f5a9" />

Afterwards, I implemented a shell script to feed the AI the prompt to parse and triage each log.

<img width="1920" height="1080" alt="Screenshot (20)" src="https://github.com/user-attachments/assets/3d61dff9-24a0-4e4d-9780-6a14348eb0a3" />

The third log entry catches the Enumeration on the Windows VM. 

<img width="1920" height="1080" alt="Screenshot (21)" src="https://github.com/user-attachments/assets/8739e2b1-7a43-4c73-9d7a-087dcb47017d" />

**7. Skills Learned**
___
```
Wazuh SIEM Administration
Sysmon Configuration and Monitoring
Endpoint Detection and Response (EDR)
Security Information and Event Management (SIEM)
Threat Detection and Analysis
MITRE ATT&CK Framework
Adversary Emulation
Incident Response
Threat Hunting
Log Analysis
AI-Assisted Security Analysis
Security Operations Center (SOC) Workflows
```

**8. Conclusion** 
___

This lab was an essential hands-on experience. This training environemnt is authentic in mutliple aspects; threat detection using SIEM tools, adversary simluation with MITRE Caldera, endpoint telemetry with Sysmon, and AI-assisted log triage and analysis. By integrating and using each tool, I was able to gain phsyical and pratcial experince with SOC techniques commonly used throughout the workforce.

**9. Acknowledgments**
___

A sincere thank you to the Panther NSO Club for the support, collaboration, and shared passion for cybersecurity that made this project possible. This lab wouldn't exist without the community you've built.

