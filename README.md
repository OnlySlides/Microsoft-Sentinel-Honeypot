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

Create Azure account and set up the virtual machine as it can take a while to process. The point of this lab is to make the VM very discoverable and ensure it does not drop any traffic. Therefore, we are removing default firewall rules and creating a new inbound rule that will allow all traffic from the internet into the VM. 

<div align="center"
  <img src="https://i.imgur.com/cw3ApsZ.png">
</div>

<img src="https://i.imgur.com/cw3ApsZ.png" width="300" img align="center">

drag & drop screenshots here or use imgur and reference them using imgsrc

Every screenshot should have some text explaining what the screenshot is about.

Example below.

*Ref 1: Network Diagram*
