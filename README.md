# Comprehensive FortiGate Next-Generation Firewall (NGFW) & Network Infrastructure Portfolio

[![GNS3 Verified](https://shields.io)](https://gns3.com)
[![FortiOS Version](https://shields.io)](https://fortinet.com)

Welcome to my portfolio of advanced, hands-on network security architectures. This repository documents a comprehensive series of multi-zone security environments designed, engineered, and validated inside **GNS3** utilizing **FortiGate NGFW appliances**. 

These projects emulate production enterprise architectures, showcasing modern methods of secure access provisioning, infrastructure redundancy, resilient dynamic routing, and encrypted edge transit.

---

## 🛠️ Core Engineering Competencies Demonstrated

### 🔐 Next-Generation Firewalling & Policies
* **Granular Micro-Segmentation:** Isolating internal LAN assets, exposed DMZ public services, untrusted WAN paths, and dedicated management domains.
* **Identity-Based Policy Filtering:** Moving beyond easily spoofed IP rules by tying firewall traffic authorization to explicit Layer 2 hardware (MAC) groupings.
* **Modern NAT Deployments:** Implementation of Central NAT architectures, decoupled from security policy views for enterprise-scale maintainability.

### 🔄 High Availability & Business Continuity
* **Deterministic Clustering:** Active-Passive state synchronization designed with manual override control parameters to establish strict primary/secondary operational roles.
* **Seamless Session Failover:** Link monitoring and path metrics tuned to guarantee continuous flow survival during unannounced device or path outages.

### 🚏 Core Networking & Dynamic Infrastructure
* **Automated Route Propagation:** Multi-zone OSPF domain configurations using loopback virtualization to distribute internal and external routes reliably.
* **Centralized Network Provisioning:** Deployed local interface DHCP scopes with rigid IP-to-MAC reservations alongside enterprise-grade multi-hop DHCP Relay Agents.
* **Encrypted Perimeter Transit:** Formulating robust Site-to-Site IPsec cryptographic tunnels to link isolated regional office topologies securely over an untrusted WAN public cloud.

---

## 📚 Advanced Security Frameworks Studied (Theory Overview)
Beyond structural lab layouts, the following system operations and engine architectures were mastered for infrastructure hardening:
* **Deep SSL/SSH Traffic Inspection:** Implementing certificate authority chains to decrypt and inspect payload attributes, mitigating blind spots in encrypted streams.
* **UTM Security Profile Pipelines:** Deploying flow/proxy-based Antivirus engines, Web Filtering categorization profiles, DNS query blocks, and advanced Intrusion Prevention System (IPS) signature groups.
* **Denial of Service (DoS) Hardening:** Constructing network interface threshold policies to identify and drop volumetric anomalies before system resources exhaust.

---

## 🗺️ Architectural Lab Matrix

| Lab Subfolder | Focus Area | Key Architectural Component | Status |
| :--- | :--- | :--- | :--- |
| **`01-High-Availability-HA/`** | System Redundancy | Active-Passive FortiGate Clustering with Override Steering | Active ✅ |
| **`02-Mac-Address-Filtering/`** | Access Control | Layer 2 Hardware Address Security Constraints | Active ✅ |
| **`03-Central-NAT-DMZ/`** | Perimeter Edge | Central NAT Policies & Static VIP DMZ Mapping | Active ✅ |
| **`04-DHCP-Server-Reservations/`** | Client Management | Interface DHCP Scopes with Static Node Reservations | Active ✅ |
| **`05-DHCP-Relay-Agent/`** | Infrastructure Routing | Multi-hop DHCP Broadcast Forwarding across L3 Nodes | Active ✅ |
| **`06-OSPF-Routing-LAN-DMZ/`** | Dynamic Routing | Multi-Area OSPF with Segmented Security Zoning | Active ✅ |
| **`07-Site-to-Site-IPsec-VPN/`** | Secure Interconnect | WAN Cloud Traversal via Multi-Site Cryptographic Tunnels | Active ✅ |

# Lab 01: Active-Passive FortiGate High Availability (HA) Clustering

## 📌 Objectives
* Establish hardware-level infrastructure redundancy utilizing FortiGate Clustering Protocol (FGCP).
* Enforce a deterministic primary unit path by configuring cluster override mechanics.

## 🗺️ GNS3 Lab Topology
<img width="1519" height="640" alt="Active-Passive HA" src="https://github.com/user-attachments/assets/981baf0b-f855-494d-a4b9-76624580f6aa" />

---

## 🖥️ Web GUI Configuration Pathway

### Step 1: Accessing HA Clustering Panel
In the FortiGate Web UI, navigate to the main tree menu on the left:
* Go to **System** ➡️ **High Availability**

### Step 2: Formulating Cluster Settings (Primary Unit)
Configure the primary firewall unit parameters inside the setup dashboard:
* **Mode:** `Active-Passive`
* **Device Priority:** `200` *(Higher number wins master role election)*
* **Group Name:** `Corporate-HA-Cluster`
* **Password:** `secret_ha_pass`
* **Heartbeat Interfaces:** Select `port4` and set interface priority to `50`.

### Step 3: Setting Up the Secondary Node
Log into your second FortiGate appliance, navigate to **System** ➡️ **High Availability**:
* Replicate all values exactly as above, but change the **Device Priority** to `100`.

### 💡 Enabling Override Steering via Console Widget
By default, FortiOS clusters prioritize device **Uptime** over your configured **Priority** numbers. To force the cluster to always fall back to the primary unit as soon as it recovers, open the **`>_ CLI Console`** widget in the top utility bar of the GUI and run:

```fortinet
config system ha
    set override enable
end
```

---

## 📈 Verification & Status Dashboard
Navigate back to **System** ➡️ **High Availability** on your master firewall dashboard to verify that both systems show up as fully operational and synchronized.

# Lab 02: Layer 2 Security Policies & MAC Address Access Control

## 📌 Objectives
* Enforce granular micro-segmentation at the ingress firewall interface layer.
* Bind authorization profiles explicitly to hardware identity signatures rather than easily spoofed IPs.

## 🗺️ GNS3 Lab Topology & Object Definitions
<img width="1546" height="556" alt="Adress for policy for specific user" src="https://github.com/user-attachments/assets/1114ba78-d413-4e77-aaa0-9ce4c2a0f996" />

---

## 🖥️ Web GUI Configuration Pathway

### Step 1: Generating the Custom MAC Object Address
Navigate to the object construction window:
* **Policy & Objects** ➡️ **Addresses** ➡️ Click **Create New** ➕ ➡️ Select **Address**

Configure the creation pane properties explicitly using the `Device (MAC Address)` subtype as shown in the layout screenshot panel:
* **Name:** `LAN-PC1-MAC`
* **Type:** Select `Device (MAC Address)` from the drop-down menu
* **MAC address:** `00:50:79:66:68:00`
* **Interface:** `port2`

### Step 2: Provisioning the Ingress Access Policy
Apply the hardware constraint profile inside your traffic processing rules:
* Go to **Policy & Objects** ➡️ **Firewall Policy** ➡️ Click **Create New** ➕
* **Incoming Interface:** `port2` (LAN)
* **Outgoing Interface:** `port3` (WAN)
* **Source:** Select your newly generated `LAN-PC1-MAC` object block
* **Destination:** `all`
* **Action:** `ACCEPT`
* **NAT:** Toggle the slider switch to **Enabled**

# Lab 03: Central NAT Implementation & Static DMZ Mapping

## 📌 Objectives
* Deconstruct translation paths out of primary security matching frameworks via Central NAT features.
* Create a secure static Virtual IP (VIP) to bridge and publish an internal DMZ service.

## 🗺️ GNS3 Lab Topology
<img width="1556" height="630" alt="CNAT lab" src="https://github.com/user-attachments/assets/555b32f4-808f-4760-8c0d-9a0909b11f93" />

---

## 🖥️ Web GUI Configuration Pathway

### Step 1: Activating Central NAT System Visibility
Before provisioning rules, Central NAT visibility flags must be activated via system toggles:
* Go to **System** ➡️ **Feature Visibility**
* Locate **Central NAT** inside the Additional Features column ➡️ Toggle slider switch to **Enabled** ➡️ Click **Apply**

### Step 2: Mapping the Destination Inbound VIP
Publish your DMZ server asset out to external edges securely:
* Go to **Policy & Objects** ➡️ **Virtual IPs** ➡️ Click **Create New** ➕ ➡️ Select **Virtual IP**
* **Name:** `DMZ-Public-VIP`
* **Interface:** `port3` (WAN)
* **External IP Address:** `192.168.1.10`
* **Mapped IP Address:** `20.0.0.2` (Target isolated DMZ Node mapped towards the Central NAT definition)

### Step 3: Organizing the Source NAT Transform Layout
Decouple outbound source transformations completely from standard rule properties:
* Go to **Policy & Objects** ➡️ **Central SNAT** ➡️ Click **Create New** ➕
* **Incoming Interface:** `port2`
* **Outgoing Interface:** `port3`
* **Source Address / Destination Address:** Set both matching components to `all`

# Lab 04: Local DHCP Server & Static IP Address Reservations

## 📌 Objectives
* Establish localized dynamic resource onboarding parameters directly on the interface boundaries.
* Bind persistent client devices reliably to static identity IP profiles.

## 🗺️ GNS3 Lab Topology
<img width="843" height="618" alt="DHCP LAB" src="https://github.com/user-attachments/assets/2688c4e4-1724-40ea-bee9-7ca4656951a0" />

---

## 🖥️ Web GUI Configuration Pathway

### Step 1: Configuring Interface Parameters and the DHCP Pool
Deploy local interface attributes and client onboarding rules:
* Go to **Network** ➡️ **Interfaces** ➡️ Select **`port2`** and click **Edit**
* **Addressing Mode:** Set to `Manual`
* **IP/Network Mask:** `172.16.1.10/255.255.255.0`
* **DHCP Server:** Toggle configuration switch to **Enabled**
* **Address Range:** Configure pool markers to start at `172.16.1.20` and end at `172.16.1.100`

### Step 2: Adding a Static MAC Reservation Lease
Lock client node allocations to permanent structural addresses:
* Scroll to the bottom of the interface panel ➡️ Expand the **Advanced** drop-down parameters menu.
* Locate the **Mac Address Reservation** table section grid area ➡️ Click **Create New** ➕
* **MAC Address:** `00:50:79:66:68:01`
* **IP Address:** Type `172.16.1.11` *(Reserved this specific IP on the firewall to lock the client PC2 profile)*

# Lab 05: Multi-Hop Enterprise DHCP Relay Agent Integration

## 📌 Objectives
* Forward dynamic Layer 2 discover broadcast frames across non-contiguous Layer 3 interfaces cleanly to a centralized corporate server node.

## 🗺️ GNS3 Lab Topology
<img width="1371" height="657" alt="DHCP Relay LAB" src="https://github.com/user-attachments/assets/814ba058-4c08-4fc7-b117-eedaac4610f5" />

---

## 🖥️ Web GUI Configuration Pathway

### Step 1: Provisioning the Interface Relay Service
Steer dynamic request tracking structures across network boundaries out to core server assets:
* Go to **Network** ➡️ **Interfaces** ➡️ Select internal client interface **`port2`** and click **Edit**
* Locate the **DHCP Server** zone parameters ➡️ Change the activation mode selection checkbox setting from **Server** over to **Relay** instead.
* **DHCP Server IP:** Input your target corporate tracking core location address: `192.168.1.1`
* Click **OK** to apply changes.

# Lab 06: Multi-Area OSPF Dynamic Routing across Security Zones

## 📌 Objectives
* Orchestrate automated routing table updates using dynamic routing frameworks.
* Segregate route updates cleanly across LAN and WAN edge layers.

## 🗺️ GNS3 Lab Topology
<img width="1557" height="640" alt="OSPF" src="https://github.com/user-attachments/assets/30e6615e-c838-4b43-ba84-e22eceb3ad8e" />

---

## 🖥️ Web GUI Configuration Pathway

### Step 1: Enabling Dynamic Advanced Routing Tools
Ensure dynamic routing management paths are visible inside the left-hand console dashboard menu:
* Go to **System** ➡️ **Feature Visibility**
* Find **Advanced Routing** inside the option matrix layout grid ➡️ Toggle the feature slider switch to **Enabled** ➡️ Click **Apply**

### Step 2: Provisioning the OSPF Routing Backbone Engine
Configure core protocol identity numbers and segment parameters:
* Go to **Network** ➡️ **OSPF**
* **Router ID:** Set to virtual tracking marker address `1.1.1.1`

### Step 3: Defining Areas and Local Interfaces Networks
Map structural topology blocks out to active route distribution lists:
* Under the **Areas** table section grid, click **Create New** ➕ ➡️ Set Area ID parameter to core identifier `0.0.0.0`
* Under the **Networks** block list area, click **Create New** ➕:
  * Add Subnet: `172.16.10.0/24` mapped directly into Area `0.0.0.0`
  * Add Subnet: `192.168.1.0/24` mapped directly into Area `0.0.0.0`
* Click **Apply** to run the live dynamic routing daemon.

# Lab 07: Secure Site-to-Site IPsec VPN Tunneling

## 📌 Objectives
* Establish encrypted site-to-site cryptographic overlay networks across simulated untrusted public internet routes.

## 🗺️ GNS3 Lab Topology
<img width="1288" height="412" alt="Site to Site VPN" src="https://github.com/user-attachments/assets/a17bec7b-c8ac-49da-942b-ef47c82b6a8f" />

---

## 🖥️ Web GUI Configuration Pathway

### Step 1: Navigating the Built-in IPsec Setup Wizard
Formulate secure tunnel backbones easily using integrated template wizards:
* Go to **VPN** ➡️ **IPsec Wizard**

### Step 2: Defining VPN Setup Properties
* **Name:** `HQ-to-Branch-Tunnel`
* **Template type:** Select `Site to Site`
* **Remote Device Type:** Select `FortiGate` ➡️ Click **Next**

### Step 3: Establishing Authentication Details
* **IP Address:** Input your targeted branch external public tracking interface IP: `192.168.229.135`
* **Outgoing Interface:** `port1` (WAN Connection link)
* **Pre-shared Key:** Input your corporate shared vault backbone key phrase string: `encrypted_backbone_key` ➡️ Click **Next**

### Step 4: Structuring Routing and Subnet Local/Remote Definitions
* **Local Interface:** `port2` (LAN network fields automatically populated as `50.0.0.0/24`)
* **Remote Subnets:** Type target remote subnets explicitly: `60.0.0.0/24`
* Click **Create** to automatically build all underlying static route paths and object rules!

---

## 🧠 Core Engineering Knowledge & Conceptual Mastery

Beyond the structural network layer topologies deployed inside the GNS3 virtual environment, I have developed a strong theoretical foundation and comprehensive architectural knowledge of FortiOS next-generation security features and deep packet inspection. 

I possess a deep engineering understanding of how to manage, analyze, and optimize the following enterprise security frameworks:

### 🔬 Deep Packet Analysis & Verification
* **Wireshark Packet Analysis:** Extensive knowledge of capturing and parsing raw network traffic waveforms to trace handshake behaviors, diagnose dynamic routing updates, and validate cryptographic handshakes.
* **Protocol Analysis:** Command over utilizing Wireshark filters to isolate traffic anomalies, inspect DHCP lease allocations, trace OSPF hello intervals, and troubleshoot security policy match behaviors down to the byte layer.

### 🔐 Next-Generation Security Profiles & Threat Mitigation
* **Security Profiles Features:** Architectural understanding of how next-generation protection engines process traffic inspection pipelines.
* **Advanced Profile Engineering:** Knowledge of configuring and tuning security posture profiles, including:
  * **Anti Virus Profile:** Sandbox integration, heuristic analysis, and malware signature updates.
  * **Web Filter Profile:** Content category blocking, URL filtering, and corporate compliance enforcement.
  * **DNS Filter Profile:** Malicious domain mitigation, botnet command-and-control (C2) blocking, and DNS translation security.
  * **DOS Profile:** Protecting interface boundaries against volumetric flood attacks using rate-limiting thresholds.
  * **IPS Engine (Intrusion Prevention System):** Deep packet signature matching and anomalous protocol behavior detection.

### 🔑 Advanced Traffic Inspection & Identity Architectures
* **SSL Decryption & In-Depth SSL Inspection:** Comprehensive knowledge of certificate authority (CA) trust chains, proxy-based vs. flow-based decryption mechanics, and mitigating encrypted visibility blind spots safely.
* **Different Types of User Authentication:** Strategic familiarity with organizing corporate access control utilizing local databases, LDAP directory synchronization, RADIUS infrastructure, and Fortinet Single Sign-On (FSSO).
* **Traffic Analysis and Logging:** In-depth understanding of syslog architectures, event classifications, security lifecycle tracking, and generating security compliance logs.
