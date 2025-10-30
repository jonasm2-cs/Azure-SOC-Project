# Azure SOC Project

## Objective
This Azure Security Operations Center (SOC) project is aimed to establish a controlled environment for simulating and detecting cyber attacks. The primary focus is to setup a SOC in Azure, using a free Azure subscription. Then we will create a Virtual Machine (VM) that will be open to the internet to act as a honeypot. from there we will be forwarding logs to a central repository. We will then intergrate Microsoft Sentinel to analyze real-world attack data.



## Tools Used
<p>

  <img src="https://img.shields.io/badge/-Microsoft%20Sentinel-5C2D91?style=for-the-badge&logo=microsoft&logoColor=white"/>
  <img src="https://img.shields.io/badge/-Azure-0078D4?&style=for-the-badge&logo=Microsoft%20Azure&logoColor=white" />


</p>

## Table of Contents
- <a href="https://github.com/jonasm2-cs/Active-Directory-Project/blob/main/README.md#p1-logical-diagram">P1. Logical Diagram</a>
- <a href="https://github.com/jonasm2-cs/Active-Directory-Project/blob/main/README.md#p2-setting-up-our-cloud-environment">P2. Setting up our Cloud Environment</a>
- <a href="https://github.com/jonasm2-cs/Active-Directory-Project/blob/main/README.md#p3-install-and-setup-active-directory">P3. Install and Setup Active Directory</a>
- <a href="https://github.com/jonasm2-cs/Active-Directory-Project/blob/main/README.md#p4-configuring-splunk">P4. Configuring Splunk</a>
- <a href="https://github.com/jonasm2-cs/Active-Directory-Project/blob/main/README.md#p5-integrate-slack--shuffle-for-automation">P5. Integrate Slack & Shuffle for Automation</a>
- <a href="https://google.com">Demo</a>



## P1. Logical Diagram
<p><b>Objective:</b> Create a Logical Diagram using <a href ="https://app.diagrams.net/">draw.io </a> to visualize what our project will look like.</p>
_insert logical diagram_
<b>Watch Tutorial:</b> <a href="https://www.youtube.com/watch?v=1nX6_Nlly-4&ab_channel=MyDFIR"> Cybersecurity Project: Active Directory 2.0 | Part 1 </a>

## P2. Setting up our Cloud Environment
<p><b>Objective: </b> Create 3 Virtual machines using Azure (Cloud platform) and ensure VMs can communicate with each other. By the end we will have 2 Windows Servers (1 Domain Controller, 1 Test machine) and 1 Ubuntu Server which will be hosting our Splunk SIEM.</p>

### Prequisites
1. [Azure](https://azure.microsoft.com/en-ca/pricing/purchase-options/azure-account) account - If you are a student go [here](https://azure.microsoft.com/en-us/free/students). <strong>Note: This can be done on-prem but there will be more steps involved</strong>
2. [PuTTy](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) - This tool provides us with a Graphical User Interface (GUI) to connect via SSH to our Splunk Ubuntu Server - *This is optional you can use powershell to connect to it aswell!*


### Step 1: Creating our Virtual Machines on Azure
1. Once you are in - click on *Virtual Machines*
2. Click **Create** > **Virtual Machine**
3. Create a resource group and name it.
4. Change Virtual Machine Name > Region (Choose region closest to you) > Image > Size (see below for specifications)
5. Fill in Username and Password fields - *Save it for later*
6. Inbound port rules: Select: Allow Selected ports > Select: RDP (3389) - *Change this to SSH(22) on the Ubuntu (Splunk) Server*
7. Create new Virtual Network > Keep Subnet default
8. Repeat for other VMs
9. Test Functionality

### Step 2: Configuring Windows Firewall Settings
1. Connect to a Virtual Machine via Remote Desktop Protocol (RDP) or Bastion (on the Azure Portal)
2. Once you are in > Open **Windows Firewall with Advanced settings**
3. Go to **Inbound Rules** > **New Rule (Right-hand side)**
4. Change the following:
    - **Rule Type:** Custom
    - **Program:** All programs
    - **Protocol and Ports**
      - Protocol Type: ICMPv4
    - **Scope:** Any IP address
    - **Action:** Allow the Connection
    - **Profile:** Domain, Private, Public
    - **Name:** AllowICMP
6. Repeat for other Virtual Machine
7. Verify Functionality using the *ping ip address* command in the Command prompt

Since both VMs are under the same virtual network, our VMs will not be expose through the internet

<b>Watch Tutorial for Troubleshooting:</b> <a href="https://www.youtube.com/watch?v=nNvD9IcdXLA&ab_channel=Dataverax"> Azure Network Ping Problems and ICMP </a>

### Step 3: Configuring our Splunk Server
1. Conenct to the Ubuntu server using PuTTY or the Powershell Command: ```ssh username@public ipaddress```
2. Type the command: ```ip a``` to get the IP address
3. Verify connectivity between the other Virtual Machines using the command: ```ping ipaddress```

Since all VMs are under the same Virtual Network, our Splunk server should connect seamlessly

| Machine | Spcifications |
|-------------------|--------------------|
| ADLAB-ADDC1 (Domain Controller) | <ul><li>VM Size: Standard B2s</li><li>CPU: 2vCPU</li><li>RAM: 4GB</li><li>Storage: 128GB</li><li>Operating System: Windows Server 2022</li><li>Inbound Ports: RDP(3389)</li><li>Virtual Network: ADLAB-vnet</li></ul> |
| ADLAB-TEST (Testing Machine)   | <ul><li>VM Size: Standard B1ms</li><li>CPU: 1vCPU</li><li>RAM: 2GB</li><li>Storage: 64GB</li><li>Operating System: Windows Server 2022</li><li>Inbound Ports: RDP(3389)</li><li>Virtual Network: ADLAB-vnet</li></ul> |
| ADLAB-SPLUNK(Splunk Server)     | <ul><li>VM Size: Standard D2sv3</li><li>CPU: 2vCPU</li><li>RAM: 8GB</li><li>Storage: 160GB</li><li>Operating System: Ubuntu 22.04</li><li>Inbound Ports: SSH(22)</li><li>Virtual Network: ADLAB-vnet</li></ul> |
<img width="693" height="248" alt="image" src="https://github.com/user-attachments/assets/62d974f7-558b-4db3-a8a3-70e8efa15112" />



## P3. Install and Setup Active Directory
<p><b>Objective: </b> Install and configure Active Directory on one of the Servers, Promote it to a domain controller and have our target machine join our domain </p>

### Step 1: Installing Active directory
1. Connect to ADLAB-ADDC1 (Domain Controller)
2. In Server Manager > Click on Add Roles and features > hit next till youve reached the Server Roles section
3. Under Server Roles section select **Active Directory Domain Services (ADDS)** > Add feature when prompt
4. Click on Install when you've reached the **Confirmation** section

### Step 2: Domain Controller Promotion
1. on the upper right-hand corner > click on  the icon with the Warning symbol
2. Click on **Promote this server to a domain controller**
3. on the Deployment Configuration section >Select **Add a new forest** > Change Root Domain name: *ADLAB.local*> Next
4. Domain Controller Options > Apply a Strong Password > next
5. Keep everything default > Install and Restart the Domain Controller

### Step 3: Create New User
1. Open Server manager > Top Right Corner Click-on Tools > Active Directory User and Computers
2. Click on Domain > Users > Right-click > New > User
3. Fill in the Fields > Next
4. Apply a Password > check the *password never expires* box > Finish

### Step 4: Domain Join a Computer
1. On the Test Machine > Open Settings
2. System > About > Advanced System Settings
3. Computer name tab > Change > Domain: ADLAB

## P4. Configuring Splunk 
## P5. Integrate Slack & Shuffle for Automation

## Demo

## Skills Learned
[Bullet Points - Remove this afterwards]

- Advanced understanding of SIEM concepts and practical application.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Enhanced knowledge of network protocols and security vulnerabilities.
- Development of critical thinking and problem-solving skills in cybersecurity.
