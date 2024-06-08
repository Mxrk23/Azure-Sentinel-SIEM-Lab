<h1>Azure - Azure Sentinel Map with Live Cyber Attacks| SIEM </h1>

 

<h2>Description</h2>
In this project, I will be walking through how I set up Microsoft Azure Sentinel and connected it to a virtual machine with no security configuration essentially acting as a honeypot. I will then monitor and log real live remote desktop brute force attacks from different IP addresses from around the world and will take that data and display it on a world map to show where  the attacks are coming from. I will use a powershell script that will look up the attacker’s geolocation data which will be plotted on the Microsoft Sentinel map. My first steps involve setting up an Azure account, a vulnerable virtual machine and a log analytics workspace where all the logs from the virtual machine will be logged and stored. This will all be in the same resource group. Once these first steps are completed, I will create custom logs which will contain the geodata ingested of the attackers.
<br />

<h2>Overview</h2>

- Create a VM in Azure. I will turn the external firewall off and the windows firewall off to make it exposed to the internet and allow anyone in any country to ping the VM. I'm doing this to entice attackers and and for it to be discovered really quickly

- Create a log repository in Azure (Log analytics Workspace). I will use this to ingest the logs from the virtual machine
  
- Setup Azure Sentinel (SIEM). I'm going to use this to create a map that will show all the attackers data e.g what regions  the attackers are coming from.

- Use Powershell to transform logs between the VM and log repository. Powershell will be used to extract IP addresses from the windows log to a third party API (I will be using Ipgeolocation). This API will derive the geographic  information (e.g latitude, longitude, state, country) which will be sent back to my Virtual machine which will then use it to create a custom log with geographic data.



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
<br>In my Virtual Machine, I’ve set my inbound rules in a way that it is open for everything to the public internet. Protocol is set to any and action is made allow therefore enabling all traffic from the internet with a low priority setting. This is going to essentially allow traffic from the internet to my Virtual Machine enticing attackers to attempt access to the VM.<br>
  

<img src="https://i.imgur.com/TKV1pxU.png" height="80%" width="80%" alt="Azure Sentinel Map with Live Cyber Attacks"/>

<p align="center">
Setting up Log Analytics Workspace: <br/>
Whilst my VM is being deployed, I will be creating my log analytics workspace. The purpose of this is to ingest logs from my VM (the windows event logs) into the workspace. I am also going to create my own custom log that will contain geographic information that discovers where the attackers are coming from.<br/>

<img src="https://i.imgur.com/ufPezUD.png" height="80%" width="80%" alt="Azure Sentinel Map with Live Cyber Attacks"/>

<p align="center">
Gathering VM Logs: <br/>
My next step is enabling the gathering of VM logs in the Security Center. I’ve done this by turning Microsoft Defender on to give me an enhanced view of the security posture of my VM. Data collection rules were also created to collect all security events and logs and to be saved and logged in my log analytics workspace. This will be used later<br>
<img src="https://i.imgur.com/ufPezUD.png" height="80%" width="80%" alt="Azure Sentinel Map with Live Cyber Attacks"/>
<img src="https://i.imgur.com/KiR7nWC.jpeg" height="80%" width="80%" alt="Azure Sentinel Map with Live Cyber Attacks"/>

<p align="center">
Observing Event Viewer logs in VM: <br/>
I’ve logged in to my virtual machine via remote desktop using the public IP shown in the overview of my honeypot vm. Before logging into the VM, I’ve purposely made some failed login attempts which will be observed in the event viewer. This is because I will gain a better understanding of how my security logs will work and be able to use the ip addresses and the geodata produced from the failed login attempts to create a custom log which will be sent to my log analytics workspace to be used on sentinel that I will further use to plot data points of failed RDP logins on a world map.  <br/>

<img src="https://i.imgur.com/fcRAbfp.jpeg" height="80%" width="80%" alt="Azure Sentinel Map with Live Cyber Attacks"/>
<img src="https://i.imgur.com/UOH2TCW.jpeg" height="80%" width="80%" alt="Azure Sentinel Map with Live Cyber Attacks"/>


<p align="center">
Event 4625: <br/>
Event 4625 shows all login failures, Through viewing event 4625 we can see the details of the login failure such as the account name being used, the reason why the login failed and the source network address of where the login was attempted <br/>
<img src="https://i.imgur.com/VzJRZ9L.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>

<p align="center">
IpGeolocation API: <br/>
Since the information in the security event only provides the IP addresses and not the location of the failed login, I will programmatically with PowerShell , take the IP address and use IPGeolocation API to find the location. From the information captured from one of the IP addresses, I’m going to create a custom log and will send the log to my log analytics workspace in azure. Once this is completed, I’m going to use sentinel (SIEM) to read the latitude and longitude and other geographic data to plot out attackers on a world map.<br/>
<img src="https://i.imgur.com/FVfGSPp.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>


<p align="center">
Turning off VM firewalls: <br/>
Back on my Virtual Machine, I’ve turned the firewalls off just to make it susceptible and respond to icmp echo requests. This will allow people to discover the VM on the internet with ease.<br/>
<img src="https://i.imgur.com/5kR7XW4.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>

<p align="center">
Ping: <br/>
I’ve left the VM and opened up a terminal on my macbook to start pinging requests to confirm if the VM is accessible through the internet. did have to reconfigure my security inbound rule settings on azure and extend the port range to allow ICMP traffic to my honeypot vm.<br/>
<img src="https://i.imgur.com/Dmp7CLM.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>

<p align="center">
Downloading Powershell Script and Getting Geolocation API Key: <br/>
This is the powershell script that I’ve downloaded. I’ve sourced this from Github but used my own API key from Ipgeolocation (this was attained by making an account with ipgeolocation). The API key is simply used  to attain location data e.g latitude, longitude, country location information. .<br/>
<img src="https://i.imgur.com/nwUllzQ.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>


<p align="center">
Run script to get Geo data from attackers: <br/>
Now that I have my API key and Powershell script. I’ve gone back to my virtual machine and opened up Windows Powershell ISE and run the script. When the script is running, it will look through the event security logs and will grab all the events of people who failed to login to the VM and will grab their IP addresses and will use the geodata to create a new log file.<br/>
<img src="https://i.imgur.com/Zkh4Vwq.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>

<p align="center">
Log files: <br/>
The Log file gets outputted to the C:\ProgramData. Since there is no log file already located in the folder, when the script runs it will automatically create a default log file with sample records. This will be used later to train my Log analytics workspace to accept and parse out my custom logs.<br/>
<img src="https://i.imgur.com/WRw1qLJ.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>

<p align="center">
Running the Script: <br/>
The purple output shows when there is a failed log on occurrence. As the script is running, it will look through the event viewer for the failed logins (mainly event id:4625) and it takes it out and sends them to the API ip address. The powershell code is then used to interact with the IP Geolocation API geodata used which will give me the attackers geographical location.<br/>
<img src="https://i.imgur.com/AH8YE6m.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>

<p align="center">
Display of Failed RDP logons: <br/>
This is the failed logon log file the script has created. I am going to use the first few lines which are sample data of the geodata to train my log analytics workspace. Some of these logons are failed logins myself when I previously made purposeful failed logon attempts. The rest are real failed logins made by attackers around the world. This will later be displayed on a world map using Microsoft Sentinel workbooks <br/>
<img src="https://i.imgur.com/pqJc871.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>


<p align="center">
Creating a custom log in Azure Log Analytics Workspace: <br/>
I’ve gone back to my log analytics workspace on Azure to create a custom log which will allow me to bring in the custom log with the geodata into my log analytics workspace. Before doing this, I had to save the custom logs produced in the VM into a txt file on my macbook so I can output the sample data on azure. I’ve called the custom log query FAILED_RDP_GEO_CL. <br/>
<img src="https://i.imgur.com/S8J3Rce.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>


<p align="center">
SecurityEvent: <br/>
Once I’ve created the custom log it will take a while for the log analytics workspace and virtual machine to sync and bring the logs to the analytic workspace. However, for now I can run a SecurityEvent KQL just to show all the windows event log entries. <br/>
<img src="https://i.imgur.com/12gW2j7.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>

<p align="center">
FAILED_RDP_GEO_CL: <br/>
I’ve now ran the Failed_RDP custom log and it has displayed all of my sample logs. I’ll be focusing on the raw data column as it contains my custom log entries. I will take this raw data to create a few fields from it. <br/>
<img src="https://i.imgur.com/86DF3qm.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>

<p align="center">
Creating with fields with KQL from the custom data: <br/>
This is the KQL script I’ve created and used to extract the custom data and create fields from the raw custom logs. Below the script we can see the log data is organsied according to specific fields. <br/>
<img src="https://i.imgur.com/9nvwTGs.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>

<p align="center">
Setting up a map with Microsoft Sentinel Workbook: <br/>
I will now go over to Microsoft Sentinel Workbook to setup a map with the geodata produced. The KQL script is similar to the one I used to extract fields with but I chose to exclude the sample data to be brought in so the map that will be created will only display real world failed logons.. <br/>
<img src="https://i.imgur.com/zp5lksT.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>

<p align="center">
Viewing the geodata: <br/>
I’ve used the visualation tool to view the data in a world map. We can see where the majority of failed logons is located through the heat map and the numerical data displayed below the world map which shows how many times a country tried to initiate access to the virtual machine e.g Belarus has the most attempted logons with over 1400. <br/>
<img src="https://i.imgur.com/WNl2Kzo.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>

<p align="center">
Running the VM for Live Brute Force Attacks: <br/>
I left the map on auto refresh with the VM still running for a few hours. As we can see Pakistan has surpassed Belarus for the most failed RDP logons and other countries such as India and Germany has gotten involved in attempting to logon to the VM. <br/>
<img src="https://i.imgur.com/K9OJq4p.jpeg" alt="Azure Sentinel Map with Live Cyber Attacks"/>

<p align="center">
Conclusions: <br/>
My takeaway from conducting this live cyber attack is that as soon as anything gets put on the internet there will be people who will try to gain access to it. It doesn’t matter who you are, they will use bots to scour the internet for hosts even if you don’t have anything that has value. Because of how vigorous the attackers are in attempting to gain access to anything on the internet, it is better to not use basic login information. This is because the attempted logins collected from SecurityEvent display the attackers using common usernames and passwords“administrator”. It is better to use passwords that are complex and fit the password strength criteria such as using numbers, special characters, uppercase, lowercase characters. This will add another layer of defence to your system. It hasn’t even been over 24 hours since the VM has been live and It already has over 4k attempted logins which demonstrates how ferocious attackers are in attempting to gain unauthorised access to a computer put on the internet. This also highlights the importance of using strong credentials, installing security updates and enabling multi factor authentication on every system that is on the internet. 
<br/>








 


<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
