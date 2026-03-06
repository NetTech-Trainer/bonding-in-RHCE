---

# Linux Network Bonding Configuration (nmcli)

Network Bonding allows multiple network interfaces to be combined into a single logical interface. This improves **fault tolerance**, **load balancing**, and **network performance**.

This guide demonstrates how to configure **Network Bonding using `nmcli` (NetworkManager CLI)** on Linux.

---

# What is Network Bonding?

Network Bonding is a Linux kernel feature that aggregates multiple network interfaces into one logical interface.

Benefits:

* Network **redundancy**
* **Load balancing**
* **High availability**
* **Increased throughput**

Example:

```
enp1s0 \
        > bond0 (Logical Interface)
enp8s0 /
```

---

# Bonding Modes

| Mode | Name          | Description                                        |
| ---- | ------------- | -------------------------------------------------- |
| 0    | balance-rr    | Round-robin load balancing                         |
| 1    | active-backup | One interface active, backup takes over on failure |


---

# Step 1: Check Available Network Interfaces

```bash
nmcli device
```

Example Output:

```
DEVICE  TYPE      STATE      CONNECTION
enp1s0  ethernet  connected  enp1s0
enp8s0  ethernet  disconnected --
lo      loopback  connected  lo
```

---

# Step 2: Remove Existing Network Connections

```bash
nmcli connection delete enp1s0
```

Verify again:

```bash
nmcli device
```

---

# Step 3: Create Bond Interface

Create a bonding interface named **bond0**.

```bash
nmcli connection add type bond ifname bond0 con-name bond0 bond.options "mode=balance-rr"
```

---

# Step 4: Add Slave Interfaces

Add physical interfaces as members of the bond.

```bash
nmcli connection add type ethernet ifname enp1s0 master bond0
```

```bash
nmcli connection add type ethernet ifname enp8s0 master bond0
```

---

# Step 5: Verify Bond Configuration

```bash
nmcli device
```

Example Output:

```
DEVICE  TYPE      STATE      CONNECTION
bond0   bond      connected  bond0
enp1s0  ethernet  connected  bond-slave-enp1s0
enp8s0  ethernet  connected  bond-slave-enp8s0
```

---

# Step 6: Configure IP Address

Assign IP configuration to the bond interface.

### Set IP Address

```bash
nmcli connection modify bond0 ipv4.addresses 10.0.0.30/24
```

### Set Gateway

```bash
nmcli connection modify bond0 ipv4.gateway 10.0.0.1
```

### Configure DNS

```bash
nmcli connection modify bond0 ipv4.dns "10.0.0.10 10.0.0.11"
```

### Configure DNS Search Domain

```bash
nmcli connection modify bond0 ipv4.dns-search "srv.world"
```

### Set Manual IP Method

```bash
nmcli connection modify bond0 ipv4.method manual
```

---

# Step 7: Restart Network

Reboot the system to apply changes.

```bash
reboot
```

---

# Step 8: Verify Bond Status

Check bonding status.

```bash
cat /proc/net/bonding/bond0
```

Example:

```
Bonding Mode: load balancing (round-robin)

Slave Interface: enp1s0
MII Status: up

Slave Interface: enp8s0
MII Status: up
```

---

# Step 9: Verify IP Configuration

```bash
ip address show
```

Example:

```
bond0: <BROADCAST,MULTICAST,MASTER,UP>
inet 10.0.0.30/24
```

---

# NetworkManager Configuration Files

Network bonding configurations are stored in:

```
/etc/NetworkManager/system-connections/
```

Example:

```
bond0.nmconnection
bond-slave-enp1s0.nmconnection
bond-slave-enp8s0.nmconnection
```

---

# Useful Commands

### Show network devices

```
nmcli device
```

### Show connections

```
nmcli connection
```

### Check bonding status

```
cat /proc/net/bonding/bond0
```

---

# Requirements

* Linux system with **NetworkManager**
* At least **2 network interfaces**
* Root or sudo privileges

---

# Author

**Sagar**

Linux | AWS | DevOps | Cloud Enthusiast

---

