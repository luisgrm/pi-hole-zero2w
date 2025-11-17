# 02 — Network Configuration

## Overview

In this section, you will assign a static DHCP lease to your Raspberry Pi Zero 2 W from your router’s admin panel and verify that the Pi is reachable using both its hostname (`pi-zero2w.local`) and its assigned IP address (`192.168.1.50`).

You will also confirm SSH access using the static IP to ensure a stable connection for the upcoming Pi-hole installation.

---

## Assigning a Static DHCP Lease

Instead of configuring a static IP directly in the Raspberry Pi, the most reliable method is to set a **DHCP reservation** in your router. This ensures:

- The Pi always receives the same IP (`192.168.1.50`)
- Centralized management
    - The router controls IP assignment, eliminating the need to manually configure or maintain static IP settings inside the device
- No IP conflicts on the network  
- Pi-hole’s DNS service remains stable  

### Steps (generalized):

1. Open your router’s admin page (commonly `192.168.1.1`)
    - If you don’t know the address, check the sticker on your router or contact your Internet Service Provider’s technical support.
2. Navigate to **LAN**, **DHCP Reservation**, or **Static Leases**
3. Obtain the current dynamic IP:
    - Either run on the Raspberry Pi: 
    ```bash 
    ifconfig
    ``` 
    - or locate the device named `pi-zero2w` in the router’s device list.
4. Copy the Pi’s MAC address (listed in the router interface)
5. Assign the static IP:  
   `192.168.1.50`
6. Save the settings in the router
7. Reboot the Raspberry Pi if needed

![Screenshot - Router DHCP reservation page with Pi assigned to IP 192.168.1.50](../images/router_dhcp_lease.png)

---

## Verifying the Static IP Assignment

After your Raspberry Pi reboots, confirm it received the correct IP:

### Check the local IP address from the Pi:

```bash
hostname -I
```

You should see:

```plaintext
192.168.1.50
```

![Screenshot - Terminal showing hostname -I output with 192.168.1.50](../images/connectivity_test_ping.png)

---

## Connectivity Tests

### Ping the Pi using the static IP

From your macOS terminal:

```bash
ping -c 4 192.168.1.50
```

This confirms the Pi is reachable over the network using its reserved IP.

### Ping the Pi using its hostname

```bash
ping -c 4 pi-zero2w.local
```

This confirms that hostname resolution is functioning properly.

![Screenshot - Ping tests using both static IP and hostname](../images/hostname_ip_check.png)

---

## SSH Confirmation (Using Static IP Only)

Now that the Pi has a stable IP, connect to it via SSH using:

```bash
ssh luisgrm@192.168.1.50
```

Using the static IP is required because Pi-hole will function as your network's DNS server, and DNS services must have a consistent IP address.

![Screenshot - SSH session to luisgrm@192.168.1.50](../images/ssh_static_ip.png)

---

## Checkpoint

By the end of this section, your Raspberry Pi Zero 2 W should have:

- A static IP address assigned (`192.168.1.50`)
- Successful ping responses using static IP
- Successful ping responses using hostname (`pi-zero2w.local`)
- Successful SSH access using the static IP

Your device is now fully ready for **[03-pihole-installation.md](03-pihole-installation.md)**.