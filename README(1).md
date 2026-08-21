<div align="center">

# 🔐 Cybersecurity Lab Environment Setup

**Building an isolated virtual lab for penetration testing and ethical hacking practice**
</div>

<p align="center">
  <img src="https://img.shields.io/badge/Skill-Cybersecurity-404040?style=flat-square&labelColor=C00000" />
  <img src="https://img.shields.io/badge/Ver-QEMU%2FKVM%20%2B%20virt--manager-0070C0?style=flat-square&labelColor=000000" />
  <img src="https://img.shields.io/badge/Kali%20Linux-vX.X-E87500?style=flat-square&labelColor=000000&logo=kalilinux&logoColor=white" />
  <img src="https://img.shields.io/badge/Skill-Linux-404040?style=flat-square&labelColor=C00000" />
  <img src="https://img.shields.io/badge/Network-192.168.122.0%2F24-238F89?style=flat-square&labelColor=000000" />
  <img src="https://img.shields.io/badge/Penetration%20Testing-C00000?style=flat-square&labelColor=000000&logo=kalilinux&logoColor=white" />
  <img src="https://img.shields.io/badge/Skill-Virtualization-404040?style=flat-square&labelColor=C00000" />
  <img src="https://img.shields.io/badge/GitHub-404040?style=flat-square&labelColor=0070C0&logo=github&logoColor=white" />
  <img src="https://img.shields.io/badge/Kali%20Linux-404040?style=flat-square&labelColor=C00000&logo=kalilinux&logoColor=white" />
  <img src="https://img.shields.io/badge/Ethical%20Hacking-E87500?style=flat-square&labelColor=000000&logo=kalilinux&logoColor=white" />
</p>

---

## 📌 Project Overview

This project focuses on setting up a **virtual cybersecurity and penetration-testing laboratory** using **QEMU/KVM** with **virt-manager** as the graphical front end, and **Kali Linux** as the guest OS.

The purpose of the lab is to create a controlled environment where cybersecurity tools, network scanning, reconnaissance, vulnerability assessment, and other security-testing activities can be performed safely and repeatedly.

The lab is configured on an isolated virtual network so that additional machines can be added later and used as targets for authorized security testing.

---

## 🎯 Objectives

The main objectives of this project are to:

- Install and configure QEMU/KVM and virt-manager (libvirt stack).
- Install/import Kali Linux as a virtual machine.
- Create or use an isolated **libvirt virtual network** for the lab.
- Configure network connectivity for the Kali VM.
- Assign a consistent IP address to the Kali VM.
- Verify network connectivity and DNS resolution.
- Take a clean VM snapshot for recovery.
- Document the complete setup process.
- Prepare the environment for future cybersecurity projects.

---

## 🛡️ Purpose of the Lab

The lab provides an isolated and controlled environment for cybersecurity learning and authorized security testing.

It can be used for activities such as:

- Network reconnaissance
- Port scanning
- Vulnerability assessment
- Packet analysis
- Web security testing
- Exploitation practice
- Security-tool experimentation

⚠️ **Important:** This laboratory must only be used for systems that you own or have explicit permission to test. Do not use the lab or its tools to attack unauthorized systems.

---

## 🏗️ Lab Architecture

![](1-screenshot-title-image.png)

Additional target machines can be added to the same libvirt virtual network in future projects.

---

## ⚙️ Lab Configuration

| 🧩 Component       | ⚙️ Configuration               |
| ------------------ | ------------------------------ |
| 🖥️ Host OS         | *(fill in — e.g. Ubuntu 24.04)* |
| 🧠 Host RAM        | *(fill in)*                    |
| ⚡ Processor       | *(fill in)*                    |
| 🧰 Hypervisor      | QEMU/KVM (libvirt) + virt-manager |
| 🐉 Security OS     | Kali Linux *(version)*         |
| 🧠 Kali RAM        | *(fill in, e.g. 2048 MB)*      |
| 🌐 Virtual Network | libvirt NAT network (`default` or custom) |
| 📡 Network Address | 192.168.122.0/24 *(replace with your actual subnet)* |
| 🐧 Kali IP Address | *(fill in, e.g. 192.168.122.x)* |
| 🚪 Default Gateway | 192.168.122.1 *(replace if custom)* |
| 🌍 DNS Server      | 8.8.8.8 or libvirt's dnsmasq   |
| 🔮 Future VM Range | *(fill in, e.g. .3–.99)*       |

> 📝 Run `ip a` and `virsh net-dumpxml <network-name>` on your actual setup and replace the placeholders above with the real values before submitting.

---

# 🪜 Lab Setup Procedure

## Step 1. Install QEMU/KVM and virt-manager

The KVM hypervisor, libvirt daemon, and virt-manager GUI were installed on the host.

**Tools:** `qemu-kvm`, `libvirt-daemon-system`, `libvirt-clients`, `bridge-utils`, `virt-manager`

Example (Debian/Ubuntu-based hosts):
```bash
sudo apt update
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager
sudo usermod -aG libvirt,kvm $USER
```
*(Verify exact package names for your specific distro before running — they can differ, e.g. on Fedora/Arch.)*

---

## Step 2. Verify Hardware Virtualization Support

Before creating any VM, hardware virtualization extensions (Intel VT-x / AMD-V) must be enabled in BIOS/UEFI and visible to the host kernel.

```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
```
A result greater than `0` indicates virtualization support is available.

---

## Step 3. Configure the libvirt Virtual Network

Rather than VirtualBox's "NAT Network," libvirt provides its own virtual network feature, managed via `virsh` or the virt-manager GUI (Edit → Connection Details → Virtual Networks).

By default, libvirt ships with a `default` NAT network on `192.168.122.0/24` with DHCP enabled. You can use this default network, or define a dedicated one for the lab.

Example: checking the active network
```bash
virsh net-list --all
virsh net-dumpxml default
```

![](2-screenshot-network-settings-1.png)

A libvirt NAT network was used because multiple VMs attached to the same virtual network can communicate with one another while also retaining outbound internet connectivity — equivalent in purpose to VirtualBox's NAT Network.

This allows future attacker and target VMs to communicate within the lab.

---

## Step 4. Import Kali Linux

The Kali Linux virtual machine image (pre-built qcow2/OVA, or installed from the official ISO) was imported into virt-manager.

The VM network adapter was configured as follows:

```text
Network source: Virtual network 'default' (NAT)
Device model:   virtio (or e1000 for legacy compatibility)
```

The VM was allocated:

```text
RAM: (fill in, e.g. 2048 MB)
vCPUs: (fill in)
```

![](3-screenshot-kali-linux.png)

A shared folder (via `virtiofs`, 9p filesystem passthrough, or a mounted shared directory) was also configured for transferring files between the host and the Kali VM, if needed.

---

## Step 5. Configure the Kali Linux Network

The Kali Linux network configuration was checked and, if needed, set to a consistent IPv4 address (static or DHCP reservation).

Example configuration:

```text
IP Address: (fill in, e.g. 192.168.122.x)
Subnet Mask: 255.255.255.0
Gateway: 192.168.122.1
DNS: 8.8.8.8
```

A consistent IP address makes it easier to document the lab and reference the Kali machine in future exercises.

![](4-screenshot-kali-network-settings.png)

---

## Step 6. Create a Clean VM Snapshot

After completing the initial configuration, a snapshot was created using virt-manager's Snapshots tab, or via `virsh`.

Example via CLI:
```bash
virsh snapshot-create-as kali-linux "Clean-Kali-Network-Setup" \
  --description "Baseline after initial network configuration"
```

The snapshot represents the clean baseline of the laboratory. If a future exercise changes or damages the VM configuration, the machine can be reverted with:
```bash
virsh snapshot-revert kali-linux "Clean-Kali-Network-Setup"
```
*(I'm not fully certain of every flag/edge case for `virsh snapshot-*` across libvirt versions — check `virsh help snapshot-create-as` on your system, or use the virt-manager GUI, which handles this more reliably for disk-based VMs.)*

---

# 🔎 Lab Verification

| ✅ Test                        | 🧾 Command                      | 🎯 Expected Result              |
| ----------------------------- | -------------------------------- | -------------------------------- |
| 🌐 Check IP address           | `ip a`                           | Correct Kali IP displayed        |
| 📡 Test gateway               | `ping <gateway-ip>`              | Successful replies               |
| 🌍 Test Internet connectivity | `ping 8.8.8.8`                   | Successful replies               |
| 🔎 Test DNS resolution        | `nslookup networkwalks.com`      | Domain resolves                  |
| 🧰 Verify Nmap                | `nmap --version`                 | Nmap version displayed           |
| 🔄 Verify snapshot            | Revert snapshot, then run `ip a` | Baseline configuration restored  |

### Example Results

```text
IP Address:
(fill in)/24

Gateway:
(fill in)

DNS:
8.8.8.8
```

---

# 🐞 Problems Encountered & Solutions

Documenting problems is an important part of the project.

## Problem 1. Internet Connectivity After Static IP Configuration

After manually configuring IPv4 settings, Internet connectivity may fail depending on the Kali/NetworkManager configuration.

One workaround used during this lab was:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
```

The network connection was then restarted/rebooted and connectivity was tested again.

> **Important:** Network interface and connection names may differ between systems. Identify your actual connection name first with `nmcli connection show` before running an `nmcli` command.

## Problem 2. KVM Acceleration / Virtualization Not Available

The VM may fail to start, or run extremely slowly, if hardware virtualization (Intel VT-x / AMD-V) is disabled in firmware, or if the `kvm` kernel module isn't loaded.

The issue was resolved by:

1. Restarting the computer.
2. Entering BIOS/UEFI settings.
3. Enabling Intel VT-x / AMD-V (hardware virtualization).
4. Saving the configuration and restarting.
5. Confirming the KVM module is loaded: `lsmod | grep kvm`.
6. Starting the Kali VM again.

*(If the issue persists, checking `dmesg` for KVM-related errors and confirming the user is in the `libvirt`/`kvm` groups is a reasonable next step — verify against your distro's docs if this doesn't resolve it.)*

---

# 💡 What I Learned

Through this project, I learned how to create and configure a virtual environment for cybersecurity practice using the libvirt/QEMU-KVM stack.

The most important concepts I learned include:

### 1. libvirt NAT Networks vs. Other Modes

A libvirt NAT network allows multiple VMs connected to the same virtual network to communicate with one another while providing address translation for external connectivity — conceptually similar to VirtualBox's "NAT Network" mode, as opposed to VirtualBox's plain "NAT" (which isolates each VM).

This makes it useful for building a multi-machine cybersecurity laboratory.

### 2. Virtual Machine Networking

I learned how libvirt virtual network adapters connect virtual machines to different network types (NAT, bridged, isolated) and how network configuration affects communication between machines.

### 3. Static IP Configuration

I learned how to configure and verify IPv4 addressing, subnet masks, gateways, and DNS settings in Kali Linux.

### 4. VM Snapshots

I learned that a clean snapshot should be created **before performing risky or experimental activities**, using either virt-manager's Snapshots tab or `virsh snapshot-create-as`.

This provides a known-good recovery point for future cybersecurity exercises.

### 5. Documentation

I learned that documenting commands, configuration, screenshots, problems, and solutions is an important part of a professional cybersecurity project.

---

# 🔐 Security & Ethical Use

This laboratory is intended strictly for educational purposes only.

---

# 🔗 Tools & Resources

- **QEMU/KVM:** [https://www.qemu.org/](https://www.qemu.org/)
- **virt-manager:** [https://virt-manager.org/](https://virt-manager.org/)
- **libvirt:** [https://libvirt.org/](https://libvirt.org/)
- **Kali Linux:** [https://kali.org/get-kali](https://kali.org/get-kali)

---

# 👤 Author

**(Your name)**
Cybersecurity Student

---

## 📌 Project Information

**Program/Course:** *(fill in)* | **Week:** *(fill in)* | **Project:** Cybersecurity & Pentesting Lab Setup | **Repository:** GitHub
