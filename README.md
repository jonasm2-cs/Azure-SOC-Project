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


## P3. Creating VMs in Azure 
### Step 1: Creating our Resource group
<img width="1045" height="225" alt="image" src="https://github.com/user-attachments/assets/7a669bcb-f647-449e-a9aa-3fd46a27d6a4" />

1.  on the Home page > Click on Resource groups > Create
2.  Subscription: Azure subscription 1
3.  Name: RG-SOC-LAB
4.  Region: (US) East US 2

### Step 2: Creating our Virtual Network
<img width="1072" height="225" alt="image" src="https://github.com/user-attachments/assets/3f43e06a-61f7-41be-82f8-e6ffd07c8826" />

1. on the Home Page > Search for "Virtual Networks > Click Create
2. Subscription: Azure Subscription 1
3. Resource Group: RG-SOC-LAB
4. Virtual Network Name: VNET-SOC-LAB
5. Region: (US) East US 2
6. Keep defaults > Review + create

## Step 3: Creating our Virtual Machine
<img width="1088" height="175" alt="image" src="https://github.com/user-attachments/assets/ac5a59e0-3b26-40e8-80b0-16e5e9341a7d" />

1. On the Home page > click on Virtual Machines > Create Virtual Machines
2. Basics
  3.   Resource Group: RG-SOC-LAB
  4. Name: CORP-NET-EAST-1
  5. Image: Windows 11 Pro version, 25H2 x64 Gen 2
  6. Size: Standard_D2s_v3 - 2 vcpus, 8GiB memory
  7. Username: labuser *This can be anything
  8. Password: *this can be anything
9. Disks Tab
   - OS Disk Type: Premium SSS
10. Networking tab:
  - Virtual Network: VNET-SOC-LAB
  - Subnet: default
  - Delete public IP and NIC when VM is deleted: Check
11. Monitoring Tab:
  - Boot Diagnostics: Disable
12. Review + create > Create


| Machine | Spcifications |
|-------------------|--------------------|
| ADLAB-ADDC1 (Domain Controller) | <ul><li>VM Size: Standard B2s</li><li>CPU: 2vCPU</li><li>RAM: 4GB</li><li>Storage: 128GB</li><li>Operating System: Windows Server 2022</li><li>Inbound Ports: RDP(3389)</li><li>Virtual Network: ADLAB-vnet</li></ul> |
| ADLAB-TEST (Testing Machine)   | <ul><li>VM Size: Standard B1ms</li><li>CPU: 1vCPU</li><li>RAM: 2GB</li><li>Storage: 64GB</li><li>Operating System: Windows Server 2022</li><li>Inbound Ports: RDP(3389)</li><li>Virtual Network: ADLAB-vnet</li></ul> |
| ADLAB-SPLUNK(Splunk Server)     | <ul><li>VM Size: Standard D2sv3</li><li>CPU: 2vCPU</li><li>RAM: 8GB</li><li>Storage: 160GB</li><li>Operating System: Ubuntu 22.04</li><li>Inbound Ports: SSH(22)</li><li>Virtual Network: ADLAB-vnet</li></ul> |
<img width="693" height="248" alt="image" src="https://github.com/user-attachments/assets/62d974f7-558b-4db3-a8a3-70e8efa15112" />


## Demo

## Skills Learned

- Advanced understanding of SIEM concepts and practical application.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Enhanced knowledge of network protocols and security vulnerabilities.
- Development of critical thinking and problem-solving skills in cybersecurity.
