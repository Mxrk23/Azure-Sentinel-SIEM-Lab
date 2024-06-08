<h1>Azure - Azure Sentinel Map with Live Cyber Attacks| SIEM </h1>

 

<h2>Description</h2>
In this project, I will be walking through how I set up Microsoft Azure Sentinel and connected it to a virtual machine with no security configuration essentially acting as a honeypot. I will then monitor and log real live remote desktop brute force attacks from different IP addresses from around the world and will take that data and display it on a world map to show where  the attacks are coming from. I will use a powershell script that will look up the attacker’s geolocation data which will be plotted on the Microsoft Sentinel map. My first steps involve setting up an Azure account, a vulnerable virtual machine and a log analytics workspace where all the logs from the virtual machine will be logged and stored. This will all be in the same resource group. Once these first steps are completed, I will create custom logs which will contain the geodata ingested of the attackers.
<br />

<h2>Overview</h2>
Create a VM in Azure. I will turn the external firewall off and the windows firewall off to make it exposed to the internet and allow anyone in any country to ping the VM. I'm doing this to entice attackers and and for it to be discovered really quickly
Create a log repository in Azure (Log analytics Workspace). I will use this to ingest the logs from the virtual machine
Setup Azure Sentinel (SIEM). I'm going to use this to create a map that will show all the attackers data e.g what regions  the attackers are coming from.
Use Powershell to transform logs between the VM and log repository. Powershell will be used to extract IP addresses from the windows log to a third party API (I will be using Ipgeolocation). This API will derive the geographic  information (e.g latitude, longitude, state, country) which will be sent back to my Virtual machine which will then use it to create a custom log with geographic data.



<h2>Tools and Utilities Used</h2>

- <b>Microsoft Azure<b>
  
- <b>[IPGeolocation API](https://ipgeolocation.io)</b>

- <b>Azure Sentinel</b>

- <b>Windows Remote Desktop<b>

- <b>PowerShell<b>

- <b> Azure Logs Analytics Workspace<b>

- <b>Azure Workbooks<b>

- <b> Microsoft Data Collection Rules<b>

<h2>Environments Used </h2>

- <b>Macbook Pro<b>
- <b>Windows 11 Pro, version 22H2 - x64 Gen2 (VM) <b>



<h2>Lab walk-through:</h2>

<p align="center">
Creating Network Security Group: <br/>
  <br>
<img src="https://i.imgur.com/TKV1pxU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Select the disk:  <br/>
<img src="https://i.imgur.com/tcTyMUE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Enter the number of passes: <br/>
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Confirm your selection:  <br/>
<img src="https://i.imgur.com/cdFHBiU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Wait for process to complete (may take some time):  <br/>
<img src="https://i.imgur.com/JL945Ga.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Sanitization complete:  <br/>
<img src="https://i.imgur.com/K71yaM2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Observe the wiped disk:  <br/>
<img src="https://i.imgur.com/AeZkvFQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
