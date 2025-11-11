# Azure SOC Project

## Objective
This Azure Security Operations Center (SOC) project is aimed to establish a controlled environment for simulating and detecting cyber attacks. The primary focus is to setup a SOC in Azure, using a free Azure subscription. Then we will create a Virtual Machine (VM) that will be open to the internet to act as a honeypot. from there we will be forwarding logs to a central repository. We will then intergrate Microsoft Sentinel to analyze real-world attack data.



## Tools Used
<p>
  <img src="https://img.shields.io/badge/-Microsoft%20Sentinel-5C2D91?style=for-the-badge&logo=microsoft&logoColor=white"/>
  <img src="https://img.shields.io/badge/-Azure-0078D4?&style=for-the-badge&logo=Microsoft%20Azure&logoColor=white" />


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

### Step 2: Configuring our Sentinel Instance
1. Azure Portal > Search for "Microsoft Sentinel" > Create
2. 29:19


## Demo

## Skills Learned

- Advanced understanding of SIEM concepts and practical application.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Enhanced knowledge of network protocols and security vulnerabilities.
- Development of critical thinking and problem-solving skills in cybersecurity.
