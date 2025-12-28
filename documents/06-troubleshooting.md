# 06 — Troubleshooting

## Overview

This section covers common issues you may encounter while running Pi-hole on a Raspberry Pi Zero 2 W, along with clear steps to diagnose and resolve them.

The goal is not only to fix problems, but also to help you understand **why** they occur and how to verify that Pi-hole is functioning correctly afterward.

---

## Pi-hole Dashboard Not Loading

### Symptoms
- `http://pi.hole/admin` does not load
- Browser shows *connection refused* or *page not found*

### Checks

1. Verify the Pi is reachable:

```bash
ping -c 4 192.168.1.98
```

2. Verify the web server is running:

```bash
sudo systemctl status lighttpd
```

### Fix

If the service is not active, restart it:

```bash
sudo systemctl restart lighttpd
```

Then refresh the browser.

---

## No Ads Being Blocked

### Symptoms
- Dashboard shows **0 queries**
- Ads still appear on websites
- No activity in Query Log

### Most Common Cause
Your devices are **not using Pi-hole as their DNS server**.

### Checks

On a client device (macOS example):

```bash
nslookup google.ca
```

Expected output:

- DNS server should be `192.168.1.98`

If another IP appears, the device is bypassing Pi-hole.

### Fix

- Confirm router DNS is set to Pi-hole
- Disable IPv6 DNS on the router or configure Pi-hole IPv6 support
- Restart the router to renew DHCP leases

---

## Devices Bypassing Pi-hole Using IPv6

### Symptoms
- DNS queries appear intermittently
- `dig` shows IPv6 DNS servers
- Dashboard activity is inconsistent

### Explanation

Many routers advertise **IPv6 DNS servers**, which can override IPv4-based Pi-hole DNS unless explicitly disabled or handled.

### Fix Options

**Option A (Simplest): Disable IPv6 on the router**
- Recommended for home networks unless IPv6 is required

**Option B (Advanced): Configure Pi-hole for IPv6**
- Requires additional router and Pi-hole configuration

After changes:
- Reboot router
- Reconnect client devices

---

## Pi-hole FTL Service Not Running

### Symptoms
- Dashboard loads but shows no activity
- DNS queries fail
- Error messages in logs

### Check Service Status

```bash
sudo systemctl status pihole-FTL
```

### Fix

If inactive or failed:

```bash
sudo systemctl restart pihole-FTL
```

If issues persist, check logs:

```bash
sudo journalctl -u pihole-FTL --no-pager
```

---

## DNS Resolution Works but Internet Does Not

### Symptoms
- `nslookup` works
- `ping` to domains fails
- Browsing is slow or broken

### Likely Causes
- Incorrect upstream DNS selected
- Router firewall blocking DNS forwarding
- Temporary ISP DNS issue

### Fix

Change upstream DNS in Pi-hole:

- **Settings → DNS**
- Select a different provider (e.g., Cloudflare or Google)
- Save and retry

---

## Admin Password Not Working

### Symptoms
- Cannot log into Pi-hole web interface
- Forgot admin password

### Fix

Reset the password via SSH:

```bash
sudo pihole setpassword
```

Set a new secure password and retry login.

---

## Emergency DNS Recovery (Internet Down)

If Pi-hole becomes unreachable and your entire network loses DNS:

### Temporary Router Fix
- Set router DNS to a public resolver:
  - `1.1.1.1`
  - `8.8.8.8`

This restores connectivity immediately.

### Restore Pi-hole Later
- Fix Pi-hole (in most cases, restarting the pi will fix the problem)
- Reassign router DNS back to `192.168.1.98`

---

## Useful Diagnostic Commands

Check Pi-hole status:
```bash
pihole status
```

Restart Pi-hole services:
```bash
sudo systemctl restart pihole-FTL lighttpd
```

Update gravity lists:
```bash
sudo pihole -g
```

View real-time DNS activity:
```bash
sudo pihole -t
```

---

## Checkpoint

If all issues are resolved, you should now have:

- A reachable Pi-hole dashboard
- Active DNS query logging
- Devices consistently using Pi-hole for DNS
- Stable internet connectivity
- Confidence recovering from failures

Your Pi-hole deployment is now **fully operational, reliable, and maintainable**.