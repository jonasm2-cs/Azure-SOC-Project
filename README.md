# Azure SOC Project

## Objective
This Azure Security Operations Center (SOC) project is aimed to establish a controlled environment for simulating and detecting cyber attacks. The primary focus is to setup a SOC in Azure, using a free Azure subscription. Then we will create a Virtual Machine (VM) that will be open to the internet to act as a honeypot. from there we will be forwarding logs to a central repository that will be intergrated to Microsoft Sentinel to analyze real-world attack data.



## Tools Used
<p>
  <img src="https://img.shields.io/badge/-Microsoft%20Sentinel-5C2D91?style=for-the-badge&logo=microsoft&logoColor=white"/>
  <img src="https://img.shields.io/badge/-Azure-0078D4?&style=for-the-badge&logo=Microsoft%20Azure&logoColor=white" />
  <img src="https://img.shields.io/badge/-KQL-003C78?style=for-the-badge&logo=Microsoft%20Azure%20Data%20Explorer&logoColor=white" />



</p>

## Table of Contents
- <a href="https://github.com/jonasm2-cs/Azure-SOC-Project/blob/main/README.md#p1-logical-diagram">P1. Logical Diagram</a>
- <a href="https://github.com/jonasm2-cs/Azure-SOC-Project/blob/main/README.md#p1-logical-diagram">P2. Creating a Azure Account</a>
- <a href="https://google.com">Demo</a>



## P1. Logical Diagram
<p><b>Objective:</b> Create a Logical Diagram using <a href ="https://app.diagrams.net/">draw.io </a> to visualize what our project will look like.</p>

![Azure SOC Project (1)](https://github.com/user-attachments/assets/efafc994-cde7-4375-80da-a117fedab649)

1. Create a VM (Honeypot) that is open to the public (intentionally)
2. Allow Attackers to attack our VM
3. Configure Log forwarding to forward log into a central repository
4. Connect Repository to our Microsoft Sentinel SIEM
5. Create an attack map to showcases where attacks are coming from

## P2. Create a Free Microsoft Azure Account 
<p><b>Objective:</b> Create a free Microsoft Azure account to host our Virtual Machiness</p>
- <a href=https://azure.microsoft.com/en-us/free/student> Use this link for Student Account</a><br/>
- <a href=https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account> Use this link for Personal Account</a><br/> 


<img width="1260" height="621" alt="image" src="https://github.com/user-attachments/assets/c4d7489d-caf0-4f10-a33d-2c1b1cc7ce1d" />

  1. Click on "Try Azure for free"
  2. Fill out Profile Information
  3. Fill out Address Information
  4. Fill out Credit Card information *You will not be charged unless you move to pay-as-you go subscription plan


## P3. Creating our Azure Environment
### Step 1: Creating our Resource group
<img width="1045" height="225" alt="image" src="https://github.com/user-attachments/assets/7a669bcb-f647-449e-a9aa-3fd46a27d6a4" />

1.  on the Home page > Click on Resource groups > Create
2.  Subscription: Azure subscription 1
3.  Name: RG-SOC-LAB
4.  Region: (US) East US 2

### Step 2: Creating our Virtual Network
<img width="1068" height="224" alt="image" src="https://github.com/user-attachments/assets/522cc0a2-66ef-4c59-92e1-03e71e2736d6" />


1. on the Home Page > Search for "Virtual Networks > Click Create
2. Subscription: Azure Subscription 1
3. Resource Group: RG-SOC-LAB
4. Virtual Network Name: VNET-SOC-LAB
5. Region: (US) East US 2
6. Keep defaults > Review + create

### Step 3: Creating our Virtual Machine
<img width="1088" height="175" alt="image" src="https://github.com/user-attachments/assets/ac5a59e0-3b26-40e8-80b0-16e5e9341a7d" />

1. On the Home page > click on Virtual Machines > Create Virtual Machines
2. Basics
  - Resource Group: RG-SOC-LAB<br/>
  - Name: CORP-NET-EAST-1<br/>
  - Image: Windows 11 Pro version, 25H2 x64 Gen 2<br/>
  - Size: Standard_D2s_v3 - 2 vcpus, 8GiB memory<br/>
  - Username: labuser *This can be anything<br/>
  - Password: *this can be anything<br/>

4. Disks Tab
   - OS Disk Type: Premium SSS
5. Networking tab:
  - Virtual Network: VNET-SOC-LAB
  - Subnet: default
  - Delete public IP and NIC when VM is deleted: Check
5. Monitoring Tab:
  - Boot Diagnostics: Disable
6. Review + create > Create


| Machine | Spcifications |
|-------------------|--------------------|
| CORP-NET-EAST-1 (Honeypot VM) | <ul><li>VM Size: Standard D2s v3</li><li>CPU: 2vCPU</li><li>RAM: 8GB</li><li>Storage: 128GB</li><li>Operating System: Windows 11 Pro</li><li>Inbound Ports: RDP(3389)</li><li>Virtual Network: VNET-SOC-LAB </li></ul> |
<img width="1276" height="527" alt="image" src="https://github.com/user-attachments/assets/d117ab32-1b9f-4d9e-9f2b-7b7cd3ba6378" />

## P4. Configuring our Firewall
Objective: Open our VM so that attackers can try and access it 
<img width="1276" height="685" alt="image" src="https://github.com/user-attachments/assets/738c18a0-8b15-4e2b-b6c9-c7cfae8f6e0b" />

### Step 1: Configuring our NEtwork Security Group (Azure Firewall)
1. In our Resource Group click on CORP-NET-EAST-1
2. Under the Inbound Rule Section > Delete RDP security Rule
3. On the LEft Hand side > Settings > Inbound Security Rules > Add
4. Add Security Rule:
   - Source: Any
   - Source Port Range: *
   - Destination: Any
   - Service: Custom
   - Destination Port Ranges: *
   - Portocol: Any
   - Action: Allow
   - Priority 100
   - Name: DANGER_AllowAnyCustomAnyInbound

### Step 2: Configuring our VM Internal Firewall
<img width="1265" height="656" alt="image" src="https://github.com/user-attachments/assets/e1635320-705b-422d-af08-06a3c6dea091" />

1. In our resource Group > Click on CORP-NET-EAST-1
2. Under the "Essentials Section > Copy the Public IP Address
3. Open Remote Desktop on your Host computer > Paste public IP address > Login *This wil be different if you are running MAC
4. in our VM > Search for "wf.msc" (Windows Defender Firewall) > Windows Defender firewall Properties
5. under each Tab > Turn Firewall State: Off > Apply

### Step 3: Verify our Host can ping the VM
<img width="697" height="208" alt="image" src="https://github.com/user-attachments/assets/fbcca15d-d2da-4a58-b1b5-7d15e47d0832" />

1. Obtain public IP address
2. On your Host Computer > Open cmd > ping _ipaddress_

## P5. Viewing Raw Logs in our VM (Unfinished Section- this is also optional
<img width="2022" height="175" alt="image" src="https://github.com/user-attachments/assets/57dc43bb-2c50-42ae-9851-f0b2ee225fa6" />

1. Remote Desktop into our VM Using wrong passwords (Try this 3 times)
2. Login > Search for "Event Viewer"
3. In Event Viewer > Left-hand Side "Windows Logs" > Security
4. Left-Hand side > Filter Current Log > Event ID: 4625 - This will condese the logs into the specific Event ID (Logon Failure)

## P6. Creating our Log Repository & Forwarding Events
### Step 1: Creating Log Analytics Workspace 
1. On the Azure Portal > Search for "Log Analystics Workspaces" > Create
2. Create Log Analytics Workspace:
   - Resource Group: RG-SOC-LAB
   - Name: LAW-SOC-LAB-0000
3. Review + Create > Create

### Step 2: Creating our Microsoft Sentinel Instance
<img width="1276" height="437" alt="image" src="https://github.com/user-attachments/assets/da313472-0cce-42ca-8bc4-c505ed3dce47" />
**Replace this picture**

1. Azure Portal > Search for "Microsoft Sentinel" > Create
2. Click on "LAW-SOC-LAB-0000" > Add

### Step 3: Configuring Azure Monitoring Agent: Security Event Connector
Basically creates a connection between our VM and the Log Analytics Workspace
<img width="2559" height="711" alt="image" src="https://github.com/user-attachments/assets/5f694eec-b15e-42c5-90e4-877cd53cffd4" />

1. Go to Microsoft Sentinel > Click on LAW-SOC-LAB-0000
2. On the Left-handside Click on Content Management > Content Hub > if prompted click on "Click here to go to the Defender Portal"
3. On the Defender Portal > Click on Microsoft Sentinel (on the left-hand side) > Content Management > Content hub > Search: Windows Security Event > Check the Windows Security Events > Install/update
4. Once the installation is finished > click on Manage at the bottom
5. Open up your Azure VM on another Tab > Settings > Extensions + applications
6. Go back to the Microsoft defender portal > Under the Windows Security Events > Click on on "Windows Security Events via AMA" > Open Connector Page
7. On the Connector page > Create data Collection rule > Name: DCR-WINDOWS > Subscription: Azure Subcription 1 > Resource Group: RG-SOC-LAB > Next: Resouces > Expand and Select our VM (CORP-NET-EAST-1) > Next: Collect > Select which events to stream: All Security Events > Next: Review + Create > Next

### Step 4: Verify
<img width="1275" height="954" alt="image" src="https://github.com/user-attachments/assets/13aba5c7-18c5-467a-b4e8-69f32b7ee07a" />

1. In a new tab > Go to Log Analytics Workspace > Click on LAW-SOC-LAB-0000
2. On the Left hand side Click on Logs > Close pop-ups > Switch to KQL mode on the top right (Formerly Simple Mode)
3. On the top panel > Type "Security Event"

## P7. Uploading Geoloaction Data to Microsoft Sentinel 
<img width="1180" height="685" alt="image" src="https://github.com/user-attachments/assets/56378224-654d-4d01-a303-2f4c3fc95f34" />

### Step 1: Creating a Watchlist
1. On the Azure Portal > Open Microsoft Sentinel > Click on our Instance "LAW-SOC-LAB-0000
2. On the Left-side Panel Click on Watchlist(under Configuration) > redirect to Microsoft Defender > Click New
3.Watchlist wizard (General Tab):
   - Name: geoip
   - Alias: geoip
4. Watchlist wizard (Source Tab):
   - Browse for files > upload .csv file found in video
   - Searchkey*: network
5. Review and Create  


## P8. Creating our Windows Attack map
<img width="1355" height="740" alt="image" src="https://github.com/user-attachments/assets/be9262c0-79d1-4369-b098-6e580529bfc8" />

1. On our Azure Portal Click on Micorosoft Sentinel > Click on our Sentinel Instance LAW-SOC-LAB-0000
2. On the Left-side Panel Click on Workbooks(under Threat Management) > redirect to Microsoft Defender > Click Add a Workbook > edit > Remove > Copy this <a href=https://drive.google.com/file/d/1ErlVEK5cQjpGyOcu4T02xYy7F31dWuir/view>.json file</a>
3. Add > Add data source + visualization > Advanced editor tab > Paste the code > Apply > Done Editing
4. Save > Rename


## Demo

## Skills Learned

- Advanced understanding of SIEM concepts and practical application.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Enhanced knowledge of network protocols and security vulnerabilities.
- Development of critical thinking and problem-solving skills in cybersecurity.
