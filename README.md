
# Configuring Dynamic Trunking Protocol (DTP)

## Introduction
Welcome! In this lab, I’ll walk you through my process of configuring DTP on a simple network setup using Packet Tracer. We’re going to be working with six PCs and three switches, configuring VLANs, trunking, and ensuring proper connectivity between devices.

<p align="center">
  <img src="https://github.com/user-attachments/assets/46f024e9-9496-491d-bbc1-6d87f70c56f1" height="100%" width="100%" >
</p>

---

## Addressing Table

| Device | Interface | IP Address    | Subnet Mask      |
|--------|-----------|---------------|------------------|
| PC1    | NIC       | 192.168.10.1  | 255.255.255.0    |
| PC2    | NIC       | 192.168.20.1  | 255.255.255.0    |
| PC3    | NIC       | 192.168.30.1  | 255.255.255.0    |
| PC4    | NIC       | 192.168.30.2  | 255.255.255.0    |
| PC5    | NIC       | 192.168.20.2  | 255.255.255.0    |
| PC6    | NIC       | 192.168.10.2  | 255.255.255.0    |
| S1     | VLAN 99   | 192.168.99.1  | 255.255.255.0    |
| S2     | VLAN 99   | 192.168.99.2  | 255.255.255.0    |
| S3     | VLAN 99   | 192.168.99.3  | 255.255.255.0    |

---

## Objectives
- Configure static trunking
- Configure and verify DTP

---

## Scenario Overview
Managing VLANs and trunks in a network with multiple switches can get complex. Cisco's DTP can help simplify this by managing trunk negotiation between switches. In this exercise, I’ll configure trunk links between switches, assign ports to VLANs, and verify connectivity between devices.

---

## Step 1: Verify VLAN Configuration

First things first, I want to make sure the VLANs are correctly set up. On **S1**, I entered privileged EXEC mode and used the command:

```bash
S1# show vlan brief
```

I see that VLANs **99** (Management) and **999** (Native) are already configured, along with some default ones. Here’s what I saw on **S1**:

```
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1 - Fa0/24, Gig0/1, Gig0/2
99   Management                       active    
999  Native                           active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active   
```

I repeated this check on **S2** and **S3**, and the same VLANs are configured across all switches.

<p align="center">
  <img src="https://github.com/user-attachments/assets/74fba57a-a038-49a9-af46-018644d00176" height="100%" width="100%" >
</p>

**Question:**  
What VLANs are configured on the switches?  
**Answer:**  
VLANs 99 and 999.

---

## Step 2: Creating Additional VLANs

Next, I went ahead and added new VLANs to **S2** and **S3**. I started with **S2**:

```bash
S2(config)# vlan 10
S2(config-vlan)# name Red
```

Then I added **VLAN 20 (Blue)** and **VLAN 30 (Yellow)**:

<p align="center">
  <img src="https://github.com/user-attachments/assets/58320098-714b-43be-9e63-03a3289715b9" height="100%" width="100%" >
</p>

```bash
S2(config)# vlan 20
S2(config-vlan)# name Blue

S2(config)# vlan 30
S2(config-vlan)# name Yellow
```

I verified the new VLANs by running `show vlan brief` and confirmed that VLANs 10, 20, 30, 99, and 999 are now active on **S2**.

I followed the same process on **S3** to ensure consistency across the network.

<p align="center">
  <img src="https://github.com/user-attachments/assets/e65345a7-389a-4b2b-890a-fc2bd58e7e2b" height="100%" width="100%" >
</p>

---

## Step 3: Assigning VLANs to Ports

Now, it’s time to assign VLANs to ports. Here’s the assignment plan:

| Ports       | VLAN  | Network           |
|-------------|-------|-------------------|
| S2 F0/1-8   | 10    | 192.168.10.0/24   |
| S3 F0/1-8   | 10    | 192.168.10.0/24   |
| S2 F0/9-16  | 20    | 192.168.20.0/24   |
| S3 F0/9-16  | 20    | 192.168.20.0/24   |
| S2 F0/17-24 | 30    | 192.168.30.0/24   |
| S3 F0/17-24 | 30    | 192.168.30.0/24   |

I started by assigning **VLAN 10** to ports **F0/1-8** on **S2**:

<p align="center">
  <img src="https://github.com/user-attachments/assets/33b4f1c9-d834-40e1-9fda-eb9df4ec8977" height="100%" width="100%" >
</p>

```bash
S2(config-if)# interface range f0/1 - 8
S2(config-if-range)# switchport mode access
S2(config-if-range)# switchport access vlan 10
```

I repeated this for the other VLANs:

```bash
S2(config-if)# interface range f0/9 - 16
S2(config-if-range)# switchport mode access
S2(config-if-range)# switchport access vlan 20

S2(config-if)# interface range f0/17 - 24
S2(config-if-range)# switchport mode access
S2(config-if-range)# switchport access vlan 30
```

I mirrored this configuration on **S3** for the same port ranges.


<p align="center">
  <img src="https://github.com/user-attachments/assets/e5402804-51dc-4b0f-89a5-fa06db65b61b" height="100%" width="100%" >
</p>

---

## Step 4: Trunking Configuration

Now let’s configure the trunking links. Starting with **S1**, I set the link between **S1** and **S2** to use **dynamic desirable** mode:

<p align="center">
  <img src="https://github.com/user-attachments/assets/546112bc-cc67-4d48-9e86-20f316bdde67" height="100%" width="100%" >
</p>

```bash
S1(config)# interface g0/1
S1(config-if)# switchport mode dynamic desirable
```

I verified the configuration using the `show interfaces trunk` command on **S2**.

<p align="center">
  <img src="https://github.com/user-attachments/assets/cec69801-e2fc-4eb6-9da6-e2bfb33c61e1" height="100%" width="100%" >
</p>

**Question:**  
What will be the result of trunk negotiation between S1 and S2?  
**Answer:**  
Trunk link has been established between S1 and S2.

For the link between **S1** and **S3**, I configured a **static trunk** and disabled DTP negotiation:

<p align="center">
  <img src="https://github.com/user-attachments/assets/9e726123-5de7-4326-9aff-d00580a6bb0d" height="100%" width="100%" >
</p>


```bash
S1(config)# interface g0/2
S1(config-if)# switchport mode trunk
S1(config-if)# switchport nonegotiate
```

---

## Step 5: Native VLAN Configuration

Finally, I configured **VLAN 999** as the **native VLAN** across all trunk links on **S1**:

<p align="center">
  <img src="https://github.com/user-attachments/assets/06007467-045b-4601-b78d-f6b4444c4804" height="100%" width="100%" >
</p>

```bash
S1(config)# interface range g0/1 - 2
S1(config-if-range)# switchport trunk native vlan 999
```

I made sure to apply the same configuration on **S2** and **S3**.

<p align="center">
  <img src="https://github.com/user-attachments/assets/e67b84d4-d9e5-4969-b2ce-0307f2b3df71" height="100%" width="100%" >
</p>


<p align="center">
  <img src="https://github.com/user-attachments/assets/b4d56309-92ca-4ae1-8f92-5219ba10d209" height="100%" width="100%" >
</p>

---

## Verification and Testing

After everything was configured, I tested the network by attempting to ping from **PC1** to **PC6**. Initially, it failed, and after troubleshooting, I realized that **S3's trunk configuration** wasn’t set correctly. Once I fixed the trunking configuration on **S3**, the pings between the PCs were successful.

<p align="center">
  <img src="https://github.com/user-attachments/assets/e816f97d-b17e-4754-bd19-1539df7c47a6" height="100%" width="100%" >
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/3d8682b2-8c72-42f0-bfd8-101f842529d8" height="100%" width="100%" >
</p>

---

## Conclusion

That wraps up the DTP configuration! This process allowed us to verify VLANs, assign them to ports, configure trunking, and set a native VLAN across the network. Each step was crucial to ensuring proper communication between devices on the same VLAN.
