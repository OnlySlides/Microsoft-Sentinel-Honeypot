# Microsoft-Sentinel-Honeypot

## Objective
This guided lab is aimed to analyze real-world attack data by establishing a basic home SOC in the cloud using Azure, creating a honeypot virtual machine (VM) and allowing attackers to try and access the VM. The primary focus was to ingest and analyze logs within a Security Information and Event Management (SIEM) system, generating test telemetry and creating a report that visualizes the data. This hands-on experience was designed to deepen understanding of log analysis, threat detection, and SOC operations in a real-world cloud environment.

Credit for this guided lab goes to [Josh Madakor on Youtube](https://www.youtube.com/@JoshMadakor). 

### Skills Learned
- Creating an Azure subscription and setting up a VM within
- Configuring Log Analytics Workspace
- Forwarding logs and integrating with Sentinel
- Querying failed login attempts and visualizing attack sources
- Building an attack map to track real-time hacker activity

### Tools Used
- Azure & Sentinel (SIEM) for log ingestion & analysis, and report generation
- Windows Event Viewer to analyze security events
- PowerShell (PS) to programatically retrieve data

### Insights
This guided lab is a great example of why it is so important to understand a multitude of tools and how they intersect in order to achieve a certain result. During my self-study on HackTheBox, they use Splunk as the primary SIEM so the introduction to Microsoft's SIEM in this lab was interesting but also overwhelming. With little knowledge in PS, retrieving the specific data for the report would not have been possible without the provided script. 

### Steps

Create Azure account and set up the virtual machine as it can take a while to process. The point of this lab is to make the VM very discoverable and ensure it does not drop any traffic. Therefore, remove default firewall rules and create a new inbound rule that will allow all traffic from the internet into the VM. 
<p align="center">
New inbound rule: <br/>
<img src="https://i.imgur.com/cw3ApsZ.png" height="40%" width="40%" alt="Azure Honeypot Steps"/>
<br />

Create log analytics workspace while VM is being created. Purpose is to ingest Windows event logs and create our own custom logs containing geographic information.
<p align="center">
New log analystics workspace: <br/>
<img src="https://i.imgur.com/IlqxTrJ.png" height="50%" width="80%" alt="Azure Honeypot Steps"/>
<br />

Enable Microsoft Defender for Cloud so we can gather logs from the VM into the log analystics workspace. <br />
Navigation path: Homepage > Navigate > Microsoft Defender for Cloud
<p align="center">
Enable Defender for Cloud: <br/>
<img src="https://i.imgur.com/EV7zqRN.png" height="40%" width="60%" alt="Azure Honeypot Steps"/>
<br />
Connect log analytics workspace with VM: <br/>
<img src="https://i.imgur.com/f040CsZ.png" height="30%" width="30%" alt="Azure Honeypot Steps"/>
<br />

Using remote desktop (RDP) to login into the VM using the VM's public IP address
<p align="center">
VM's IP address: <br/>
<img src="https://i.imgur.com/vaXqTNt.png" height="80%" width="80%" alt="Azure Honeypot Steps"/>
<br />
Connect to VM using RDP: <br/>
<img src="https://i.imgur.com/rddQWQr.png" height="40%" width="40%" alt="Azure Honeypot Steps"/>
<br />
Open another RDP and purposely fail the login: <br/>
<img src="https://i.imgur.com/o9JPjqD.png" height="35%" width="35%" alt="Azure Honeypot Steps"/>
<br />

In remote desktop, open event viewer and focus on Event ID 4625 (Audit Failure). Gather all the failures that tried to log into the VM via RDP. Clicking into the event shows more details including the IP address of those that tried to log into the VM and failed.
<p align="center">
Open another RDP and purposely fail the login: <br/>
<img src="https://i.imgur.com/KLAk6yG.png" height="60%" width="60%" alt="Azure Honeypot Steps"/>
<br />
Event details: <br/>
<img src="https://i.imgur.com/JDDJzx6.png" height="20%" width="40%" alt="Azure Honeypot Steps"/>
<br />

Note that details does not show which country the IP is originating from. We will create a custom log using PS to programtically retrieve the IP addresses and using an IP geolocation API to generate more information. The custom log will be sent to the log analytics workspace in Azure, then use Sentinel (SIEM) to plot out the different attacker on the map. 

Windows firewall on the VM need to be turned off so it can respond to ICMP echo requests. This results in people discovering the VM faster on the internet. 
<p align="center">
-t for perpetual ping in command prompt (CMD): <br/>
<img src="https://i.imgur.com/cHfkzm9.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
Request is timing out so we navigate into VM Windows firewall and disable Domain & Public profile. This is a honeypot so disabling is fine.
<p align="center">
Disabling firewall in VM: <br/>
<img src="https://i.imgur.com/RCvTsVe.png" height="70%" width="70%" alt="Azure Honeypot Steps"/>
<br /> 
Ping is now working as echo requests are allowed: <br/>
<img src="https://i.imgur.com/oVSTvzu.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br /> 

Download a PS script provided for this lab. 
<p align="center">
Copy & pasting the script into PS ISE: <br/>
<img src="https://i.imgur.com/E64Z3YA.png" height="60%" width="60%" alt="Azure Honeypot Steps"/>
<br />
Saving it as Log_Exporter in the Desktop: <br/>
<img src="https://i.imgur.com/3X7vk93.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />

Get IP API key so we can convert IP addresses to logitude & latitude or country. 
<p align="center">
Retrieving IP API key: <br/>
<img src="https://i.imgur.com/7Pt60Dn.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
Copy & paste the API key into the script: <br/>
<img src="https://i.imgur.com/AAPaODX.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
<br />
Run the script in PS. 3 entries because there are only 3 failed logon's in event viewer: <br/>
<img src="https://i.imgur.com/ZFP9jp9.png" height="90%" width="90%" alt="Azure Honeypot Steps"/>
<br />
Event viewer (Event ID 4625): <br/>
<img src="https://i.imgur.com/A1jZI86.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />

Information from the failed logon is sent to the IP API. Geodata is then deposited into the failed_rdp log file.
<p align="center">
failed_rdp log file: <br/>
<img src="https://i.imgur.com/mzafjrf.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />

In the log file: "samplehost" are the sample data used to train the analytics workspace, failed logons are labeled as "honeypot-vm" 
<p align="center">
Log file data: <br/>
<img src="https://i.imgur.com/LHtRIiB.png" height="40%" width="40%" alt="Azure Honeypot Steps"/>
<br />

Create a custom log inside the log analytics workspace allowing us to bring the custom geodata log. <br /> LAW-honeypot > Tables (under settings) > Create > New custom log (MMA-based). <br /> Remember the file is on the VM, not the host device. Copy all logs in the VM and paste in new file on the host computer. 
<p align="center">
Creating a custom log in log analytics workspace: <br/>
<img src="https://i.imgur.com/nKhJJvR.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
Using the selected log to train log analytics on what to look for: <br/>
<img src="https://i.imgur.com/zgr5Lel.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
Collection path is where the log lives on the VM: <br/>
<img src="https://i.imgur.com/pc6Rqg0.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
Naming the custom log: <br/>
<img src="https://i.imgur.com/OTzyFTn.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
Review + Create: <br/>
<img src="https://i.imgur.com/IlF6Rrw.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />

After 5-10 minutes, check by querying the custom log. 
<p align="center">
Query results: <br/>
<img src="https://i.imgur.com/DzdrwMO.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
Extra details of an event. Focus on RawData: <br/>
<img src="https://i.imgur.com/1rfEoov.png" height="100%" width="100%" alt="Azure Honeypot Steps"/>
<br />
  
We need to take RawData and extract certain fields/information from it. 

Create a new workbook in Sentinel for an interactive report. <br />
Workbooks > Add Workbook > Edit > Delete current default workbooks > Add Query > copy & paste the query in (extract field no longer available in LAW) <br />
<p align="center">
Workbooks path: <br/>
<img src="https://i.imgur.com/K22nz9v.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
Copy & pasting the query script: <br/>
<img src="https://i.imgur.com/MjGlYnz.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />

Run the Query > Visualization = Map > Set map settings as Metric Label = label, Metric value = event_count > Save workbook > set map to automatically refresh every 10 minutes 
<p align="center">
Run Query configurations: <br/>
<img src="https://i.imgur.com/a4Y3AuV.png" height="100%" width="100%" alt="Azure Honeypot Steps"/>
<br />
Saving the workbook: <br/>
<img src="https://i.imgur.com/bB6Usd9.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />

We now have a map visualizing where the attackers are from! 
<p align="center">
Initial map. Majority of IP addresses are originating from Paraguay: <br/>
<img src="https://i.imgur.com/QjyJkLe.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
Adjusting map refresh timer: <br/>
<img src="https://i.imgur.com/FdIOUVX.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
After a few refreshes: <br/>
<img src="https://i.imgur.com/Jon3sH5.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />
Rate limited by the API: <br/>
<img src="https://i.imgur.com/2mfoWdf.png" height="50%" width="50%" alt="Azure Honeypot Steps"/>
<br />

## Finished!
