# Microsoft-Sentinel-Honeypot

## Objective
[Brief Objective - Remove this afterwards]

This lab is aimed to analyze real-world attack data by establishing a basic home SOC in the cloud using Azure, creating a honeypot virtual machine (VM) and allowing attackers to try and access it. The primary focus was to ingest and analyze logs within a Security Information and Event Management (SIEM) system, generating test telemetry to mimic real-world attack scenarios. This hands-on experience was designed to deepen understanding of network security, attack patterns, and defensive strategies.

### Skills Learned
[Bullet Points - Remove this afterwards]

- Advanced understanding of SIEM concepts and practical application.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Enhanced knowledge of network protocols and security vulnerabilities.
- Development of critical thinking and problem-solving skills in cybersecurity.

### Tools Used
[Bullet Points - Remove this afterwards]

- Security Information and Event Management (SIEM) system for log ingestion and analysis.
- Network analysis tools (such as Wireshark) for capturing and examining network traffic.
- Telemetry generation tools to create realistic network traffic and attack scenarios.

## Steps

Create Azure account and set up the virtual machine as it can take a while to process. The point of this lab is to make the VM very discoverable and ensure it does not drop any traffic. Therefore, remove default firewall rules and create a new inbound rule that will allow all traffic from the internet into the VM. 
<p align="center">
New inbound rule: <br/>
<img src="https://i.imgur.com/cw3ApsZ.png" height="5%" width="50%" alt="Azure Honeypot Steps"/>
<br />

Create log analytics workspace while VM is being created. Purpose is to ingest Windows event logs and create our own custom logs containing geographic information.
<p align="center">
New log analystics workspace: <br/>
<img src="https://i.imgur.com/IlqxTrJ.png" height="50%" width="80%" alt="Azure Honeypot Steps"/>
<br />

Enable Microsoft Defender for Cloud so we can gather logs from the VM into the log analystics workspace. Navigation path: Homepage > Navigate > Microsoft Defender for Cloud
<p align="center">
Enable Defender for Cloud: <br/>
<img src="https://i.imgur.com/EV7zqRN.png" height="40%" width="60%" alt="Azure Honeypot Steps"/>
<br />

<p align="center">
Connect log analytics workspace with VM: <br/>
<img src="https://i.imgur.com/f040CsZ.png" height="30%" width="40%" alt="Azure Honeypot Steps"/>
<br />

Using remote desktop (RDP) to login into the VM using the VM's public IP address
<p align="center">
VM's IP address: <br/>
<img src="https://i.imgur.com/vaXqTNt.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
Connect to VM using RDP: <br/>
<img src="https://i.imgur.com/rddQWQr.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
Open another RDP and purposely fail the login: <br/>
<img src="https://i.imgur.com/o9JPjqD.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />

In remote desktop, open event viewer and focus on Event ID 4625 (Audit Failure). Gather all the failures that tried to log into the VM via RDP. Clicking into the event shows more details including the IP address of those that tried to log into the VM and failed.
<p align="center">
Open another RDP and purposely fail the login: <br/>
<img src="https://i.imgur.com/KLAk6yG.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
<br />
Open another RDP and purposely fail the login: <br/>
<img src="https://i.imgur.com/JDDJzx6.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />

Note that details does not show which country the IP is originating from. We will create a custom log using PowerShell (PS) to programtically retrierve the IP addresses and using an IP geolocation API to generate more information. The custom log will be sent to the log analytics workspace in Azure, then use Sentinel (SIEM) to plot out the different attacker on the map. 

Windows firewall on the VM need to be turned off so it can respond to ICMP echo requests. This results in people discovering the VM faster on the internet. 
<p align="center">
-t for perpetual ping in command prompt (CMD): <br/>
<img src="https://i.imgur.com/cHfkzm9.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
Request is timing out so we navigate into VM Windows firewall and disable Domain & Public profile. This is a honeypot so disabling is fine.
<p align="center">
Disabling firewall in VM: <br/>
<img src="https://i.imgur.com/RCvTsVe.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br /> 
<p align="center">
Ping is now working as echo requestes are allowed: <br/>
<img src="https://i.imgur.com/oVSTvzu.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br /> 
