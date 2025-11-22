# Azure SOC Project

## Objective
This Azure Security Operations Center (SOC) project is aimed to establish a controlled environment for simulating and detecting cyber attacks. The primary focus is to learn how to build a foundational Security Operations Center in Azure, using a free Azure subscription. We will create a Virtual Machine (VM) that will be open to the internet to act as a honeypot. From there we will be forwarding logs to a central repository that will be intergrated to Microsoft Sentinel to analyze real-world attack data.


## Tools Used
<p>
  <img src="https://img.shields.io/badge/-Microsoft%20Sentinel-5C2D91?style=for-the-badge&logo=microsoft&logoColor=white"/>
  <img src="https://img.shields.io/badge/-Azure-0078D4?&style=for-the-badge&logo=Microsoft%20Azure&logoColor=white" />
  <img src="https://img.shields.io/badge/-KQL-003C78?style=for-the-badge&logo=Microsoft%20Azure%20Data%20Explorer&logoColor=white" />
</p>

## Resources
- <a href="https://www.youtube.com/watch?v=g5JL2RIbThM&list=PLYHfX1HJ8dv8RVatf6ULT1Ga5RaLMWreQ&index=37">Cyber Home Lab by Josh Madakor</a>
- <a href="https://docs.google.com/document/d/143seB9PwT9GSsStc14vPQWgnCHQeVMVEC6XBRz67p_Q/edit?tab=t.0">Josh Madakor Lab Checklist</a>

## Table of Contents
- <a href="https://github.com/jonasm2-cs/Azure-SOC-Project/blob/main/README.md#p1-logical-diagram">P1. Logical Diagram</a>
- <a href="">P2. Creating an Azure Account</a>
- <a href="">P3. Creating our Azure Environment </a>
- <a href="">P4. Creating our Network Security Group (Azure Virtual Firewall) </a>
- <a href="">P5. Viewing Raw Logs in our Virtual Machine (Optional) </a>
- <a href="https://github.com/jonasm2-cs/Azure-SOC-Project/tree/main?tab=readme-ov-file#p6-creating-our-log-repository--forwarding-events">P6. Creating our Log Repository & Forwarding Events </a>
- <a href="">P7. Uploading Geolocation Data to Microsoft Sentinel </a>
- <a href="https://github.com/jonasm2-cs/Azure-SOC-Project/tree/main?tab=readme-ov-file#p8-creating-our-windows-attack-map">P8. Creating our Windows Attack Map </a>
- <a href="https://github.com/jonasm2-cs/Azure-SOC-Project/blob/main/README.md#lessons-learned">Lessons Learned</a>



## Steps:
## P1. Logical Diagram
<p><b>Objective:</b> Create a Logical Diagram using <a href ="https://app.diagrams.net/">draw.io </a> to visualize what our project will look like.</p>

![Azure SOC Project (1)](https://github.com/user-attachments/assets/efafc994-cde7-4375-80da-a117fedab649)

Diagram Breakdown:
  1. Create a VM (Honeypot) that is open to the public (intentionally).
  2. Allow Attackers to attack our VM.
  3. Configure Log forwarding to forward log into a central repository (Log Analytics Workspace).
  4. Connect Repository to our Microsoft Sentinel SIEM.
  5. Create an Attack Map to showcase where attacks are coming from.

## P2. Create a Free Microsoft Azure Account 
<p><b>Objective:</b> Create a free Microsoft Azure account to host our Virtual Machines</p>
- <a href=https://azure.microsoft.com/en-us/free/student> Use this link for Student Account</a><br/>
- <a href=https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account> Use this link for Personal Account</a><br/> 

<img width="1260" height="621" alt="image" src="https://github.com/user-attachments/assets/c4d7489d-caf0-4f10-a33d-2c1b1cc7ce1d" />

  1. Click on "Try Azure for free"
  2. Fill out Profile Information
  3. Fill out Address Information
  4. Fill out Credit Card information *You will not be charged unless you move to pay-as-you go subscription plan*

## P3. Creating our Azure Environment
<p><b>Objective:</b> Create our Resource Group, Virtual Network (VNET) and Virtual Machines </p>

### Step 1: Creating our Resource group
<img width="1045" height="225" alt="image" src="https://github.com/user-attachments/assets/7a669bcb-f647-449e-a9aa-3fd46a27d6a4" />

1. On the Home page > Click on "Resource groups" > Create
2. **Basics Tab:**
   - **Subscription:** Azure subscription 1
   - **Name:** RG-SOC-LAB
   - **Region:** (US) East US 2
3. Review + Create

### Step 2: Creating our Virtual Network
<img width="1068" height="224" alt="image" src="https://github.com/user-attachments/assets/522cc0a2-66ef-4c59-92e1-03e71e2736d6" />

1. On the Home Page > Search for "Virtual Networks > Click the "Create" Button
2. **Basics Tab:**
   - **Subscription:** Azure Subscription 1
   - **Resource Group:** RG-SOC-LAB
   - **Virtual Network Name:** VNET-SOC-LAB
   - **Region:** (US) East US 2
7. Keep remaining tabs default
8. Review + Create

### Step 3: Creating our Virtual Machine
<img width="1088" height="175" alt="image" src="https://github.com/user-attachments/assets/ac5a59e0-3b26-40e8-80b0-16e5e9341a7d" />

1. On the Home page > click on "Virtual Machines" > Create Virtual Machines
2. **Basics Tab:**
   - **Resource Group:** RG-SOC-LAB
   - **Name:** CORP-NET-EAST-1
   - **Image:** Windows 11 Pro (25H2 x64 Gen 2)
   - **Size:** Standard_D2s_v3 — 2 vCPUs, 8 GiB memory
   - **Username:** labuser *(can be anything)*
   - **Password:** *(can be anything)*
4. **Disks Tab:**
   - **OS Disk Type:** Premium SSS
5. **Networking tab:**
    - **Virtual Network:** VNET-SOC-LAB
    - **Subnet:** default
    - **Delete public IP and NIC when VM is deleted:** Check
5. **Monitoring Tab:**
    - **Boot Diagnostics:** Disable
6. Review + Create > Create


| Machine | Spcifications |
|-------------------|--------------------|
| CORP-NET-EAST-1 (Honeypot VM) | <ul><li>VM Size: Standard D2s v3</li><li>**CPU:** 2vCPU</li><li>**RAM:** 8GB</li><li>**Storage:** 128GB</li><li>**Operating System:** Windows 11 Pro</li><li>**Inbound Ports:** RDP(3389)</li><li>**Virtual Network:** VNET-SOC-LAB </li></ul> |
<img width="1276" height="527" alt="image" src="https://github.com/user-attachments/assets/d117ab32-1b9f-4d9e-9f2b-7b7cd3ba6378" />

## P4. Configuring our Firewall
**Objective:** Open our VM to the internet so that attackers can try and access it 
<img width="1276" height="685" alt="image" src="https://github.com/user-attachments/assets/738c18a0-8b15-4e2b-b6c9-c7cfae8f6e0b" />

### Step 1: Configuring our Network Security Group (Azure Firewall)
1. In our Resource Group Click on CORP-NET-EAST-1
2. Under the Inbound Rule Section > Delete RDP security Rule
3. On the Left-Hand side > Settings > Inbound Security Rules > Add
4. **Add Security Rule:**
   - **Source:** Any
   - **Source Port Range:** *
   - **Destination:** Any
   - **Service:** Custom
   - **Destination Port Ranges:** *
   - **Portocol:** Any
   - **Action:** Allow
   - **Priority:** 100
   - **Name:** DANGER_AllowAnyCustomAnyInbound

### Step 2: Configuring our VM's Internal Firewall
<img width="1265" height="656" alt="image" src="https://github.com/user-attachments/assets/e1635320-705b-422d-af08-06a3c6dea091" />

1. In our resource Group > Click on CORP-NET-EAST-1
2. Under the **Essentials Section** > Copy the **Public IP Address** > Save it on a note pad
3. Open Remote Desktop on your Host computer > Paste public IP address > Login *This wil be different if you are running MAC
4. In our VM > Search for "wf.msc" (Windows Defender Firewall) > Windows Defender firewall Properties
5. Under each Tab > Turn Firewall State: Off > Apply

### Step 3: Verify our Host can ping the VM
<img width="697" height="208" alt="image" src="https://github.com/user-attachments/assets/fbcca15d-d2da-4a58-b1b5-7d15e47d0832" />

1. Obtain **Public IP address**
2. On your Host Computer > Open cmd > ping *ipaddress*

## P5. Viewing Raw Logs in our VM (Optional)
**Objective:** Interpret Raw Security logs in our Windows Virtual Machine via the built-in tool Eventviewer
<img width="2022" height="175" alt="image" src="https://github.com/user-attachments/assets/57dc43bb-2c50-42ae-9851-f0b2ee225fa6" />

1. Remote Desktop into our VM Using wrong passwords (Try this 3 times)
2. Login > Search for "Event Viewer"
3. In Event Viewer > Left-hand Side "Windows Logs" > Security
4. Left-Hand side > Filter Current Log > Event ID: 4625 - This will condese the logs into the specific Event ID (Logon Failure)

## P6. Creating our Log Repository & Forwarding Events
**Objective:** Create a Log Analytics Workspace that will hold our Windows Logs. WE will then forward them to our Microsoft Sentinel SIEM
### Step 1: Creating Log Analytics Workspace 
1. On the Azure Portal > Search for "Log Analystics Workspaces" > Create
2. Create Log Analytics Workspace:
   - **Resource Group:** RG-SOC-LAB
   - **Name:** LAW-SOC-LAB-0000
3. Review + Create > Create

### Step 2: Creating our Microsoft Sentinel Instance
<img width="1272" height="310" alt="image" src="https://github.com/user-attachments/assets/ffa055f4-cb2e-4b20-9993-072b10fe04a4" />

1. Azure Portal > Search for "Microsoft Sentinel" > Create
2. Click on "LAW-SOC-LAB-0000" > Add

### Step 3: Configuring Azure Monitoring Agent: Security Event Connector
Basically creates a connection between our VM and the Log Analytics Workspace
<img width="2559" height="711" alt="image" src="https://github.com/user-attachments/assets/5f694eec-b15e-42c5-90e4-877cd53cffd4" />

1. Go to Microsoft Sentinel > Click on "LAW-SOC-LAB-0000"
2. On the Left-handside Click on "Content Management" > Content Hub > if prompted click on "Click here to go to the Defender Portal"
3. On the Defender Portal > Click on Microsoft Sentinel (on the left-hand side) > Content Management > Content hub > Search: Windows Security Event > Check the Windows Security Events > Install/update
4. Once the installation is finished > click on "Manage" at the bottom
5. Open up your Azure VM on another Tab > Settings > Extensions + applications
6. Go back to the Microsoft defender portal > Under the Windows Security Events > Click on on "Windows Security Events via AMA" > Open Connector Page
7. On the Connector page > Create data Collection rule > Name: DCR-WINDOWS > Subscription: Azure Subcription 1 > Resource Group: RG-SOC-LAB > Next: Resouces > Expand and Select our VM (CORP-NET-EAST-1) > Next: Collect > Select which events to stream: All Security Events > Next: Review + Create > Next

### Step 4: Verify
<img width="1275" height="954" alt="image" src="https://github.com/user-attachments/assets/13aba5c7-18c5-467a-b4e8-69f32b7ee07a" />

1. In a new tab > Go to Log Analytics Workspace > Click on "LAW-SOC-LAB-0000"
2. On the Left hand side Click on "Logs" > Close pop-ups > Switch to "KQL mode" on the top right (Formerly Simple Mode)
3. On the top panel > Type "Security Event"

## P7. Uploading Geolocation Data to Microsoft Sentinel
**Objective:** Import a list of Locations to determine where our Failed login attempts are coming from.
<img width="1180" height="685" alt="image" src="https://github.com/user-attachments/assets/56378224-654d-4d01-a303-2f4c3fc95f34" />

1. Save this <a href="https://raw.githubusercontent.com/joshmadakor1/lognpacific-public/refs/heads/main/misc/geoip-summarized.csv" >list</a> as a .csv to your computer
2. On the Azure Portal > Open Microsoft Sentinel > Click on our Instance "LAW-SOC-LAB-0000"
3. On the Left-side Panel Click on "Watchlist" (under Configuration) > Redirect to Microsoft Defender > Click "New"
3.Watchlist Wizard (General Tab):
   - **Name:** geoip
   - **Alias:** geoip
4. Watchlist Wizard (Source Tab):
   - Browse for files > upload .csv file from Step 1
   - **Searchkey*:** network
5. Review and Create  


## P8. Creating our Windows Attack Map
**Objective:** Create a Visual Attack Map that showcases where Attacks are coming from. 
<img width="1355" height="740" alt="image" src="https://github.com/user-attachments/assets/be9262c0-79d1-4369-b098-6e580529bfc8" />

1. On our Azure Portal Click on "Microsoft Sentinel" > Click on our Sentinel Instance "LAW-SOC-LAB-0000"
2. On the Left-side Panel Click on "Workbooks" (under Threat Management) > Redirect to Microsoft Defender > Click "Add a Workbook" > Edit > Remove > Copy this <a href=https://drive.google.com/file/d/1ErlVEK5cQjpGyOcu4T02xYy7F31dWuir/view>.json file</a>
3. Add > Add Data Source + Visualization > Advanced editor tab > Paste the code > Apply > Done Editing
4. Save > Rename


## Lessons Learned
- Created a Resources group, Virtual Network and Subnet in Azure
- Configured an publicly available Virtual Machine (Honey Pot)
- Connected Microsoft Sentinel to Log Analytics workspace
- Utilized Kusto Query Language (KQL) to identify and analyzed failed login Attempts
- Mapped attacker IPs to visualize threat landscape

