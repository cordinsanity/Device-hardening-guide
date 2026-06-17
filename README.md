<div align="center">

<img width="100%" src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=2,3,6&height=130&section=header"/>

# Device Hardening Guide

[![Typing SVG](https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=600&size=15&duration=2500&pause=1000&color=00B894&center=true&vCenter=true&width=750&lines=VPN+%2B+Kill+Switch+%2B+WireGuard;DNS-over-HTTPS+%2B+DNSCrypt+%2B+Tor;Browser+Hardening+%2B+Anti-Fingerprinting;Full+Disk+Encryption+%2B+Secure+Boot;Underground+Linux+Distros+%2B+Anonymity+OS;Hardware+Privacy+%2B+Firmware+Security;Tor+%2B+I2P+%2B+Freenet+%2B+Overlay+Networks)](https://github.com/woodbloom/Device-hardening-guide)

> **Threat model:** Reduce passive tracking, advertising profiling, behavioral linkability, ISP surveillance, and corporate data harvesting.
> For nation-state adversaries: use Qubes OS + Whonix + air-gapped machines.

</div>

---

## Table of Contents

- [Operating Systems — Full Comparison](#-operating-systems--full-comparison)
- [Underground & Privacy Linux Distros](#-underground--privacy-linux-distros)
- [VPN — Deep Dive](#-vpn--deep-dive)
- [Tor Network — Full Setup](#-tor-network--full-setup)
- [I2P — Invisible Internet Project](#-i2p--invisible-internet-project)
- [DNS — Every Layer](#-dns--every-layer)
- [Firewall — Full Configuration](#-firewall--full-configuration)
- [Disk Encryption](#-disk-encryption)
- [Firmware & Hardware Security](#-firmware--hardware-security)
- [Browser Hardening — Complete](#-browser-hardening--complete)
- [Browser Extensions](#-browser-extensions)
- [Email Privacy](#-email-privacy)
- [Password Management](#-password-management)
- [Two-Factor Authentication](#-two-factor-authentication)
- [File Encryption](#-file-encryption)
- [Identity & Profile Separation](#-identity--profile-separation)
- [Virtualization & Sandboxing](#-virtualization--sandboxing)
- [Metadata Hygiene](#-metadata-hygiene)
- [Secure Deletion](#-secure-deletion)
- [Secure Communication](#-secure-communication)
- [Network Monitoring](#-network-monitoring)
- [System Audit & Hardening](#-system-audit--hardening)
- [Kernel Hardening](#-kernel-hardening)
- [AppArmor & SELinux](#-apparmor--selinux)
- [Common Failure Points](#-common-failure-points)
- [Quick Reference](#-quick-reference)

---

## Operating Systems — Full Comparison

| OS | Privacy | Security | Anonymity | Skill Level | Notes |
|----|---------|---------|-----------|-------------|-------|
| **Qubes OS** | ✅✅✅ | ✅✅✅ | ✅✅ | Advanced | VM per task, hardware isolation, gold standard |
| **Tails** | ✅✅✅ | ✅✅✅ | ✅✅✅ | Beginner | Amnesic live USB, everything through Tor, leaves zero trace |
| **Whonix** | ✅✅✅ | ✅✅✅ | ✅✅✅ | Intermediate | Gateway + Workstation VMs, all traffic through Tor |
| **Parrot OS** | ✅✅ | ✅✅ | ✅✅ | Intermediate | Debian-based, security/privacy tools pre-installed, lighter than Kali |
| **Kali Linux** | ✅ | ✅✅ | ✅ | Intermediate | Pentesting distro — not for daily use |
| **Fedora** | ✅✅ | ✅✅✅ | ✅ | Beginner | SELinux enforcing, great for daily use |
| **Debian** | ✅✅ | ✅✅ | ✅ | Intermediate | Rock-solid, minimal, long support cycle |
| **Arch Linux** | ✅✅ | ✅✅ | ✅ | Advanced | Complete control, minimal by default |
| **NixOS** | ✅✅ | ✅✅ | ✅ | Advanced | Declarative config, fully reproducible system |
| **Void Linux** | ✅✅ | ✅✅ | ✅ | Advanced | No systemd, runit init, minimal, musl libc option |
| **Alpine Linux** | ✅✅✅ | ✅✅✅ | ✅ | Advanced | Minimal attack surface, musl libc, used in containers |
| **Gentoo** | ✅✅ | ✅✅ | ✅ | Expert | Compile everything, custom USE flags, maximum control |
| **OpenBSD** | ✅✅ | ✅✅✅ | ✅ | Advanced | Legendary code auditing, `pledge`/`unveil` syscall restriction |
| **HardenedBSD** | ✅✅ | ✅✅✅ | ✅ | Advanced | FreeBSD fork with ASLR, PIE, RELRO everywhere |
| **Windows 11** | ❌ | ✅ | ❌ | Beginner | Heavy telemetry — harden or avoid |
| **macOS** | ⚠️ | ✅✅ | ⚠️ | Beginner | Closed source, iCloud risk, good baseline |

---

## Underground & Privacy Linux Distros

### Tails OS — The Gold Standard for Anonymity

Tails boots from USB, routes **all traffic through Tor**, and leaves **zero trace** on the machine.

```bash
# Download Tails
https://tails.boum.org/install/

# Verify signature
gpg --import tails-signing.key
gpg --verify tails-amd64-X.Y.Z.img.sig tails-amd64-X.Y.Z.img

# Write to USB (Linux)
sudo dd if=tails-amd64-X.Y.Z.img of=/dev/sdX bs=16M oflag=direct status=progress
```

**Key features:**
- Amnesic — no data persists after shutdown (unless you configure Persistent Storage)
- Tor Browser pre-installed and pre-hardened
- Tor for all traffic system-wide
- KeePassXC, MAT2, GnuPG, Kleopatra pre-installed
- Built-in MAC address spoofing

**When to use:** Sensitive research, anonymous communication, situations where the machine must leave zero evidence

---

### Whonix — Tor Workstation + Gateway Architecture

Whonix splits into two VMs:
- **Whonix-Gateway** — routes all traffic through Tor
- **Whonix-Workstation** — where you actually work, cannot leak your IP even if compromised

```
┌────────────────────────────────────────────────────┐
│                  Your Host OS                      │
│  ┌───────────────┐      ┌───────────────────────┐  │
│  │ Whonix-Gateway│ ───► │ Whonix-Workstation   │  │
│  │   (Tor exit)  │      │ (your work — apps,   │  │
│  └───────────────┘      │  browser, terminal)  │  │
│                          └───────────────────────┘  │
└────────────────────────────────────────────────────┘
```

```bash
# Install on VirtualBox / KVM
https://www.whonix.org/wiki/Download

# Verify integrity
gpg --verify Whonix-Gateway*.ova.asc
```

**Works best with Qubes OS** — in Qubes, Whonix-Gateway becomes a ProxyVM and the Workstation runs in an isolated AppVM.

---

### Qubes OS — Maximum Compartmentalization

Each task runs in its own Xen VM (called a **qube**). A malicious PDF cannot affect your banking qube.

```
┌─────────────────────────────────────────────────────┐
│                     Qubes OS                        │
│  [vault]    [work]    [personal]    [untrusted]     │
│  KeePass    Office    Browser       Suspicious      │
│  NO NET     VPN       Direct        Disposable VM   │
│                                                     │
│  [sys-net] → [sys-firewall] → [sys-whonix] → qubes │
└─────────────────────────────────────────────────────┘
```

Recommended qube setup:

| Qube | Network | Use |
|------|---------|-----|
| `vault` | None | Passwords, PGP keys — totally offline |
| `work` | VPN | Work documents, corporate tools |
| `personal` | Direct/VPN | Personal browsing |
| `anon-whonix` | Whonix-GW | Anonymous browsing |
| `untrusted` | Disposable | Opening links, attachments, unknown files |
| `sys-net` | Physical NIC | Network hardware isolation |
| `sys-usb` | USB controller | USB device isolation |

---

### Parrot OS — Privacy + Security Toolkit

```bash
# Download Parrot Security Edition
https://parrotsec.org/download/

# Parrot Home — lighter, privacy focused
# Parrot Security — full pentesting tools

# Key pre-installed tools:
# - Tor Browser, OnionShare
# - AnonSurf (routes everything through Tor)
# - MAT2 (metadata cleaner)
# - Firejail (sandbox)
# - OpenVPN, WireGuard
# - Full suite of network analysis tools
```

**AnonSurf — route everything through Tor:**
```bash
sudo anonsurf start    # Route all traffic through Tor
sudo anonsurf status   # Check status
sudo anonsurf myip     # Verify your exit IP
sudo anonsurf stop     # Restore normal routing
```

---

### Kali Linux

**Not for daily use.** Designed for penetration testing — runs as root, not hardened for everyday browsing.

```bash
# Key use cases:
# - Network auditing (nmap, masscan, netdiscover)
# - Web app testing (Burp Suite, OWASP ZAP, sqlmap)
# - Password auditing (Hashcat, John the Ripper)
# - Wireless analysis (Aircrack-ng, Kismet)
# - Exploitation (Metasploit, msfconsole)
# - Reverse engineering (Ghidra, radare2, pwndbg)
```

---

### BlackArch Linux

Arch-based with 2800+ security tools. More comprehensive than Kali for offensive security.

```bash
# Install repository on existing Arch
curl -O https://blackarch.org/strap.sh
sha1sum strap.sh  # Verify
sudo bash strap.sh

# Install all tools (large)
sudo pacman -S blackarch

# Install category only
sudo pacman -S blackarch-exploitation
sudo pacman -S blackarch-networking
sudo pacman -S blackarch-recon
```

---

### Void Linux — No Systemd, Minimal

```bash
# Void with musl libc — smallest attack surface
# https://voidlinux.org/download/

# Key advantages:
# - runit init (not systemd)
# - xbps package manager (fast, simple)
# - musl libc option (memory-safer than glibc)
# - Rolling release, minimalist
# - Very few background services by default

# Install base + hardened kernel
xbps-install -Su
xbps-install linux-hardened
```

---

### NixOS — Reproducible & Declarative

```nix
# /etc/nixos/configuration.nix
{ config, pkgs, ... }: {
  # System hardening
  security.apparmor.enable = true;
  security.sudo.execWheelOnly = true;
  boot.kernelPackages = pkgs.linuxPackages_hardened;
  
  # Firewall
  networking.firewall.enable = true;
  networking.firewall.allowedTCPPorts = [];
  
  # No mutable users
  users.mutableUsers = false;
  
  # Privacy packages
  environment.systemPackages = with pkgs; [
    tor mullvad-vpn librewolf keepassxc mat2 firejail
    wireshark tcpdump nmap lynis
  ];
  
  # Full disk encryption with LUKS2
  boot.initrd.luks.devices."cryptroot".device = "/dev/disk/by-uuid/YOUR-UUID";
}
```

---

### Alpine Linux — Minimal Attack Surface

Used in Docker containers due to tiny footprint. Excellent for servers and high-security desktops.

```bash
# Install hardened kernel
apk add linux-hardened

# Minimal init (OpenRC, not systemd)
# musl libc — fewer memory vulnerabilities
# BusyBox base — tiny

# Harden
apk add iptables fail2ban
rc-update add iptables default
rc-update add fail2ban default
```

---

### OpenBSD — The Most Secure OS

```bash
# OpenBSD philosophy: correct > fast
# Every application uses pledge() to declare what syscalls it needs
# Every application uses unveil() to declare what filesystem paths it needs
# If exploited, damage is limited to declared capabilities

# Install OpenBSD
https://www.openbsd.org/faq/faq4.html

# Enable full-disk encryption during install (softraid CRYPTO)
# Enable W^X (Write XOR Execute) — enabled by default

# Key security features:
# - ASLR (enabled)
# - Stack protector (enabled)
# - OpenSSH written by OpenBSD team (used worldwide)
# - LibreSSL (OpenSSL fork with security focus)
# - pf firewall (most powerful packet filter available)
# - securelevel — kernel protection levels
```

---

## VPN — Deep Dive

### Provider Comparison

| Provider | Jurisdiction | No-Log Audit | Payment | Kill Switch | IPv6 |
|----------|-------------|-------------|---------|------------|------|
| **Mullvad** | Sweden | ✅ Audited | Cash/Monero/Crypto | ✅ | ✅ |
| **ProtonVPN** | Switzerland | ✅ Audited | Card/Crypto | ✅ | ✅ |
| **IVPN** | Gibraltar | ✅ Audited | Cash/Monero | ✅ | ✅ |
| **AirVPN** | Italy | ✅ | Card/Crypto | ✅ | ✅ |
| **NordVPN** | Panama | ✅ Audited | Card/Crypto | ✅ | ✅ |
| **ExpressVPN** | BVI | ✅ | Card | ✅ | ✅ |
| **Avoid** | US/UK/AUS | ❌ | — | — | — |

### WireGuard — Full Setup

```bash
# Install
sudo apt install wireguard     # Debian/Ubuntu
sudo dnf install wireguard-tools  # Fedora
sudo pacman -S wireguard-tools    # Arch

# Generate keypair
wg genkey | tee /etc/wireguard/private.key | wg pubkey > /etc/wireguard/public.key
chmod 600 /etc/wireguard/private.key

# /etc/wireguard/wg0.conf
[Interface]
Address = 10.2.0.2/32, fd00::2/128
PrivateKey = <YOUR_PRIVATE_KEY>
DNS = 10.64.0.1
# Kill switch — block all non-VPN traffic
PostUp   = iptables -I OUTPUT ! -o %i -m mark ! --mark $(wg show %i fwmark) -m addrtype ! --dst-type LOCAL -j REJECT
PostUp   = ip6tables -I OUTPUT ! -o %i -m mark ! --mark $(wg show %i fwmark) -m addrtype ! --dst-type LOCAL -j REJECT
PreDown  = iptables -D OUTPUT ! -o %i -m mark ! --mark $(wg show %i fwmark) -m addrtype ! --dst-type LOCAL -j REJECT
PreDown  = ip6tables -D OUTPUT ! -o %i -m mark ! --mark $(wg show %i fwmark) -m addrtype ! --dst-type LOCAL -j REJECT

[Peer]
PublicKey = <SERVER_PUBLIC_KEY>
AllowedIPs = 0.0.0.0/0, ::/0
Endpoint = vpn.server.com:51820
PersistentKeepalive = 25

# Start
sudo wg-quick up wg0
sudo systemctl enable --now wg-quick@wg0

# Verify
wg show
curl ifconfig.me  # Should show VPN exit IP
```

### IPv6 Leak Prevention

```bash
# Disable IPv6 entirely if your VPN doesn't tunnel it
echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.d/99-privacy.conf
echo "net.ipv6.conf.default.disable_ipv6 = 1" >> /etc/sysctl.d/99-privacy.conf
sysctl -p /etc/sysctl.d/99-privacy.conf

# Or block IPv6 via ip6tables
ip6tables -P INPUT DROP
ip6tables -P OUTPUT DROP
ip6tables -P FORWARD DROP
ip6tables -A INPUT -i lo -j ACCEPT
ip6tables -A OUTPUT -o lo -j ACCEPT
ip6tables -A OUTPUT -o wg0 -j ACCEPT
```

### DNS Leak Test

```bash
curl https://dnsleaktest.com/results.html  # Or use extended test in browser
# All DNS servers shown should belong to your VPN provider
```

---

## Tor Network — Full Setup

### Tor Browser

```bash
# Download from official source only
https://www.torproject.org/download/

# Verify signature
gpg --import tor.asc
gpg --verify tor-browser-linux64-*.tar.xz.asc

# Security levels:
# Standard — JavaScript enabled, all features
# Safer    — JavaScript disabled on non-HTTPS, no WebGL
# Safest   — JavaScript disabled everywhere, no media
```

### System-wide Tor with Transparent Proxy

```bash
sudo apt install tor

# /etc/tor/torrc
VirtualAddrNetworkIPv4 10.192.0.0/10
AutomapHostsOnResolve 1
TransPort 9040
DNSPort 5353
SocksPort 9050

# iptables rules to force all traffic through Tor
# (Parrot AnonSurf does this automatically)
iptables -t nat -A OUTPUT -m owner --uid-owner debian-tor -j RETURN
iptables -t nat -A OUTPUT -p udp --dport 53 -j REDIRECT --to-ports 5353
iptables -t nat -A OUTPUT -p tcp --syn -j REDIRECT --to-ports 9040
iptables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A OUTPUT -m owner --uid-owner debian-tor -j ACCEPT
iptables -A OUTPUT -j DROP

sudo systemctl enable --now tor
```

### Tor Bridges — Bypass Tor Blocking

```bash
# If Tor is blocked in your country, use bridges
# /etc/tor/torrc
UseBridges 1
ClientTransportPlugin obfs4 exec /usr/bin/obfs4proxy
Bridge obfs4 <BRIDGE_IP>:<PORT> <FINGERPRINT> cert=... iat-mode=0

# Get bridges from:
# https://bridges.torproject.org/
# Email bridges@torproject.org
```

### OnionShare — Share Files Anonymously

```bash
sudo apt install onionshare
# Creates a temporary .onion address to share files
# No metadata, no accounts, no middleman
```

---

## I2P — Invisible Internet Project

I2P is a different anonymity network than Tor — designed for internal services rather than clearnet access.

```bash
# Install I2P (Java-based)
sudo apt install i2p i2p-router

sudo systemctl enable --now i2p

# Access I2P router console
http://127.0.0.1:7657

# I2P Browser configuration:
# HTTP Proxy: 127.0.0.1:4444
# HTTPS Proxy: 127.0.0.1:4445
```

**I2P vs Tor:**

| Feature | Tor | I2P |
|---------|-----|-----|
| Clearnet access | ✅ Exit nodes | ❌ Limited |
| Hidden services | ✅ .onion | ✅ .i2p (eepsites) |
| Design | Client anonymity | Network-wide anonymity |
| Speed | Moderate | Faster within network |
| User base | Large | Smaller |
| Best for | Anonymous browsing | Internal network services |

---

## DNS — Every Layer

### DoH, DoT, DNSCrypt Comparison

| Protocol | Port | Encrypted | Authenticated | Notes |
|----------|------|-----------|--------------|-------|
| Plain DNS | 53 | ❌ | ❌ | Your ISP sees everything |
| DoT | 853 | ✅ | ✅ | Easy to block (dedicated port) |
| DoH | 443 | ✅ | ✅ | Looks like HTTPS, hard to block |
| DNSCrypt | Various | ✅ | ✅ | Cryptographically signed resolver |
| Tor DNS | 5353 | ✅✅ | ✅✅ | Full anonymity, slow |

### Resolver Comparison

| Resolver | Logs | Ad Block | DNSSEC | Location |
|----------|------|---------|--------|---------|
| **Mullvad** | None | Optional | ✅ | Sweden |
| **NextDNS** | Optional | ✅ Custom | ✅ | Global |
| **Cloudflare 1.1.1.1** | 24h logs | Optional | ✅ | US |
| **AdGuard** | Optional | ✅ | ✅ | Cyprus |
| **Quad9** | None | ✅ Malware | ✅ | Switzerland |
| **Google 8.8.8.8** | Yes | ❌ | ✅ | US — avoid |

### systemd-resolved Full Config

```bash
# /etc/systemd/resolved.conf
[Resolve]
DNS=194.242.2.2#dns.mullvad.net 194.242.2.3#dns.mullvad.net
FallbackDNS=9.9.9.9#dns.quad9.net
Domains=~.
DNSSEC=yes
DNSOverTLS=yes
MulticastDNS=no
LLMNR=no
Cache=yes
DNSStubListener=yes

sudo systemctl restart systemd-resolved
resolvectl status
```

### DNSCrypt-proxy

```bash
sudo apt install dnscrypt-proxy

# /etc/dnscrypt-proxy/dnscrypt-proxy.toml
server_names = ['mullvad-doh', 'cloudflare']
listen_addresses = ['127.0.0.1:53']
require_dnssec = true
require_nolog = true
require_nofilter = false
block_ipv6 = false

sudo systemctl enable --now dnscrypt-proxy
```

### Verify No DNS Leaks

```bash
# Should show zero traffic
sudo tcpdump -i any -n port 53

# While browsing — if you see plain DNS on port 53, something is leaking
```

---

## Firewall — Full Configuration

### nftables (Modern Linux Firewall)

```bash
# /etc/nftables.conf
table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;

        # Allow established connections
        ct state established,related accept

        # Allow loopback
        iif lo accept

        # Allow VPN
        iif wg0 accept

        # Allow SSH (if needed — remove if not)
        tcp dport 22 ct state new limit rate 5/minute accept

        # ICMP ping
        ip protocol icmp accept
        ip6 nexthdr icmpv6 accept

        # Log and drop everything else
        log prefix "DROPPED: " drop
    }

    chain output {
        type filter hook output priority 0; policy accept;

        # Block all non-VPN outbound (kill switch)
        oif != wg0 ip daddr != 127.0.0.0/8 ip daddr != 10.0.0.0/8 reject
    }

    chain forward {
        type filter hook forward priority 0; policy drop;
    }
}

sudo nft -f /etc/nftables.conf
sudo systemctl enable --now nftables
```

### ufw — Simple Setup

```bash
sudo ufw default deny incoming
sudo ufw default deny outgoing
sudo ufw allow out on wg0    # Allow VPN interface outbound
sudo ufw allow out to <VPN_SERVER_IP> port 51820 proto udp  # Allow WireGuard handshake
sudo ufw allow in on lo
sudo ufw allow out on lo
sudo ufw enable
sudo ufw status verbose
```

### Block Telemetry via Hosts File

```bash
# /etc/hosts — mass telemetry block
0.0.0.0 telemetry.microsoft.com
0.0.0.0 vortex.data.microsoft.com
0.0.0.0 settings-win.data.microsoft.com
0.0.0.0 browser.events.data.microsoft.com
0.0.0.0 data.microsoft.com
0.0.0.0 watson.telemetry.microsoft.com
0.0.0.0 metrics.apple.com
0.0.0.0 iadsdk.apple.com
0.0.0.0 stats.adobe.com
0.0.0.0 data.flurry.com
0.0.0.0 firebaselogging.googleapis.com
0.0.0.0 app-measurement.com
0.0.0.0 crashlytics.com
```

Or use [StevenBlack hosts](https://github.com/StevenBlack/hosts) — 100,000+ blocked domains.

---

## Disk Encryption

### LUKS2 — Full Setup

```bash
# During installation — encrypt at install time (easiest)
# For existing system — encrypt home directory or specific partitions

# Create encrypted partition
cryptsetup luksFormat --type luks2 \
  --cipher aes-xts-plain64 \
  --key-size 512 \
  --hash sha512 \
  --iter-time 3000 \
  --pbkdf argon2id \
  /dev/sdX

# Verify settings
cryptsetup luksDump /dev/sdX

# Open / mount
cryptsetup open /dev/sdX cryptvault
mkfs.ext4 /dev/mapper/cryptvault
mount /dev/mapper/cryptvault /mnt/vault

# Add a backup key slot (store recovery key offline on paper)
cryptsetup luksAddKey /dev/sdX
```

### VeraCrypt — Encrypted Containers & Hidden Volumes

```bash
# Install
sudo apt install veracrypt  # or download from veracrypt.fr

# Create an encrypted container
veracrypt --create /path/to/container \
  --encryption AES \
  --hash SHA-512 \
  --filesystem ext4 \
  --size 10G \
  --password "your-passphrase"

# Hidden Volume — two passphrases, two volumes, plausible deniability
# Outer volume: innocent data, simple passphrase
# Inner hidden volume: sensitive data, separate passphrase
# Someone forcing you to reveal the password gets the outer volume only
```

### Cryptomator — Cloud Storage Encryption

```bash
# Encrypts files before they go to cloud (Dropbox, Google Drive, Nextcloud)
# AES-256-GCM, open-source, E2EE
sudo apt install cryptomator
# Or download AppImage from cryptomator.org
```

### Encrypted Swap

```bash
# /etc/crypttab
swap  /dev/sdX2  /dev/urandom  swap,cipher=aes-xts-plain64,size=256

# /etc/fstab  
/dev/mapper/swap  none  swap  sw  0  0

# Apply
sudo systemctl daemon-reload
swapon --show
```

---

## Firmware & Hardware Security

### BIOS/UEFI Hardening

```
BIOS Settings:
✅ Set BIOS password (prevents boot order changes)
✅ Enable Secure Boot (prevents unsigned bootloaders)
✅ Disable USB boot (or require password)
✅ Disable Wake-on-LAN
✅ Disable Intel ME (if your BIOS supports ME-disable)
✅ Boot order: SSD only (remove USB/network boot)
✅ Enable TPM 2.0 (for measured boot)
```

### Intel ME / AMD PSP

The Intel Management Engine is a separate CPU running proprietary firmware — it has full access to RAM, network, USB, even when the machine is "off."

```bash
# Check ME status
sudo apt install intelmetool
sudo intelmetool -s

# ME Cleaner — remove most ME modules (advanced, risky)
# https://github.com/corna/me_cleaner
# Only works on older Intel CPUs (pre-2017)

# Safer option: buy hardware with ME neutralized
# - Purism Librem laptops
# - System76 (with coreboot)
# - ThinkPads with coreboot/libreboot
```

### Coreboot / Libreboot

Replaces proprietary BIOS with open-source firmware:

```
coreboot.org — open-source firmware (some proprietary blobs)
libreboot.org — coreboot without any proprietary blobs (most private)

Supported hardware:
- ThinkPad X200, X201, X220, X230, T400, T430, T440p
- Some older Lenovo models
```

### Physical Security

```
✅ Webcam: physical cover (tape or sliding cover)
✅ Microphone: hardware kill switch, or use external USB mic
✅ Screen: privacy filter (prevents shoulder surfing)
✅ Ports: USB port blockers for unused ports
✅ Laptop: lock slot or Kensington lock cable
✅ RAM: remove unused DIMM slots or cover them
✅ HDD: encrypt AND physically destroy when disposing
```

### Hardware Kill Switches

Look for laptops with hardware kill switches:
- **Purism Librem 14** — hardware kill switches for camera, mic, WiFi/Bluetooth
- **System76 Lemur Pro** — open-source EC firmware

---

## Browser Hardening — Complete

### Browser Ranking

| Browser | Fingerprinting | Telemetry | Notes |
|---------|---------------|-----------|-------|
| **Tor Browser** | ✅ Identical for all | None | Best anonymity, slow |
| **Mullvad Browser** | ✅ Identical for all | None | Tor Browser without Tor |
| **LibreWolf** | ✅ Hardened | None | Best for daily use |
| **Brave** | ✅ Randomized | Minimal | Good, check sync settings |
| **Firefox ESR** | ⚠️ Configurable | Moderate | Requires hardening |
| **Chromium** | ⚠️ | Low | Better than Chrome |
| **Chrome** | ❌ | High | Avoid |
| **Edge** | ❌ | High | Avoid |
| **Opera** | ❌ | High | Chinese ownership — avoid |

### Complete about:config Hardening

```javascript
// === ANTI-FINGERPRINTING ===
privacy.resistFingerprinting = true
privacy.resistFingerprinting.letterboxing = true
privacy.fingerprintingProtection = true
privacy.fingerprintingProtection.overrides = "+CanvasRandomization,+ScreenResolution"

// === TRACKING PROTECTION ===
privacy.trackingprotection.enabled = true
privacy.trackingprotection.socialtracking.enabled = true
privacy.trackingprotection.cryptomining.enabled = true
privacy.trackingprotection.fingerprinting.enabled = true
privacy.trackingprotection.pbmode.enabled = true

// === FIRST-PARTY ISOLATION ===
privacy.firstparty.isolate = true

// === WEBRTC — PREVENT IP LEAKS ===
media.peerconnection.enabled = false
media.peerconnection.ice.default_address_only = true
media.peerconnection.ice.no_host = true
media.peerconnection.ice.proxy_only_if_behind_proxy = true

// === GEOLOCATION ===
geo.enabled = false
geo.provider.use_corelocation = false
geo.provider.ms-windows-location = false

// === SENSORS ===
dom.battery.enabled = false
device.sensors.enabled = false
dom.gamepad.enabled = false
dom.vr.enabled = false
dom.vibrator.enabled = false
dom.netinfo.enabled = false

// === MEDIA ===
media.navigator.enabled = false
media.autoplay.default = 5
permissions.default.camera = 2
permissions.default.microphone = 2
permissions.default.desktop-notification = 2

// === TELEMETRY (Firefox) ===
toolkit.telemetry.enabled = false
toolkit.telemetry.unified = false
toolkit.telemetry.server = "data:,"
datareporting.healthreport.uploadEnabled = false
datareporting.policy.dataSubmissionEnabled = false
app.shield.optoutstudies.enabled = false
browser.discovery.enabled = false
browser.newtabpage.activity-stream.feeds.telemetry = false
browser.newtabpage.activity-stream.telemetry = false

// === PREFETCH / SPECULATION ===
network.prefetch-next = false
network.dns.disablePrefetch = true
network.dns.disablePrefetchFromHTTPS = true
network.http.speculative-parallel-limit = 0
browser.urlbar.speculativeConnect.enabled = false
network.preload = false

// === REFERRER ===
network.http.referer.XOriginPolicy = 2
network.http.referer.XOriginTrimmingPolicy = 2
network.http.referer.trimmingPolicy = 2

// === COOKIES ===
network.cookie.cookieBehavior = 5
network.cookie.thirdparty.sessionOnly = true

// === HTTPS ===
dom.security.https_only_mode = true
dom.security.https_only_mode_ever_enabled = true
network.http.upgrade-insecure-requests = true

// === SEARCH ===
browser.search.suggest.enabled = false
browser.urlbar.suggest.searches = false
keyword.enabled = true

// === SAFE BROWSING (sends URLs to Google) ===
browser.safebrowsing.malware.enabled = false
browser.safebrowsing.phishing.enabled = false
browser.safebrowsing.downloads.enabled = false
browser.safebrowsing.downloads.remote.enabled = false

// === MISC ===
dom.event.clipboardevents.enabled = false
dom.event.contextmenu.enabled = false
network.IDN_show_punycode = true
javascript.options.wasm = false  // Disable WebAssembly if not needed
```

### Test Your Browser Fingerprint

```
https://coveryourtracks.eff.org    — fingerprint uniqueness test
https://browserleaks.com           — full leak suite (IP, WebRTC, Canvas, Fonts...)
https://amiunique.org              — cross-database fingerprint check
https://ipleak.net                 — IP, DNS, WebRTC, Torrent leaks
https://arkenfox.github.io/TZP    — timezone, locale leaks
https://www.deviceinfo.me          — detailed hardware fingerprinting
```

---

## Browser Extensions

**Minimal set — each extension increases your fingerprint surface:**

| Extension | Purpose | Priority |
|-----------|---------|---------|
| **uBlock Origin** | Ad/tracker/script blocking | 🔴 Essential |
| **ClearURLs** | Strip tracking params from URLs | 🟡 Recommended |
| **Cookie AutoDelete** | Delete cookies on tab close | 🟡 Recommended |
| **Temporary Containers** (Firefox) | Per-tab isolation | 🟡 Recommended |
| **LocalCDN** | Block CDN tracking (fonts, jQuery) | 🟢 Optional |
| **Skip Redirect** | Skip tracking redirects | 🟢 Optional |

### uBlock Origin — Advanced Config

```
Dashboard → Filter lists:
✅ uBlock filters — All
✅ EasyList
✅ EasyPrivacy
✅ Peter Lowe's Ad and tracking server list
✅ Online Malicious URL Blocklist
✅ AdGuard Annoyances
✅ Fanboy Annoyances
✅ Fanboy Social

Dashboard → My filters (custom rules):
||googletagmanager.com^
||google-analytics.com^
||doubleclick.net^
||facebook.net^$third-party
||connect.facebook.net^
||analytics.twitter.com^
||t.co^$image,third-party
||pixel.facebook.com^
||graph.facebook.com^$third-party
```

---

## Email Privacy

### Providers

| Provider | Jurisdiction | E2EE | Open Source | Notes |
|----------|-------------|------|-------------|-------|
| **ProtonMail** | Switzerland | ✅ PGP | Partial | Most popular, strong reputation |
| **Tutanota** | Germany | ✅ Proprietary | ✅ | Clean, E2EE calendar included |
| **Disroot** | Netherlands | ✅ Optional | ✅ | Community-run |
| **RiseUp** | USA | ✅ Optional | ✅ | Activist-focused, invite-only |
| **Posteo** | Germany | ✅ Optional | ❌ | Accepts cash payment |

### Email Aliases (Never Give Out Your Real Address)

| Service | Free Addresses | Notes |
|---------|---------------|-------|
| **SimpleLogin** | 10 | Open-source, Proton-owned |
| **AnonAddy** | Unlimited (limited bandwidth) | Open-source, self-hostable |
| **Firefox Relay** | 5 | Firefox integration |
| **33mail** | Unlimited | Older service |

```bash
# For every signup: use alias@simplelogin.com
# If that service gets breached/spammed — disable that alias
# Your real email is never exposed
```

### PGP — End to End Encryption

```bash
# Generate key
gpg --full-gen-key
# Type: RSA and RSA | 4096 bits | No expiry (or 2 years)

# List your keys
gpg --list-secret-keys

# Export your public key (share with contacts)
gpg --armor --export your@email.com > pubkey.asc

# Import someone's key
gpg --import theirkey.asc

# Encrypt a message
gpg --encrypt --armor --recipient their@email.com message.txt

# Sign + encrypt
gpg --sign --encrypt --armor --recipient their@email.com message.txt

# Decrypt
gpg --decrypt message.asc

# Verify a signature
gpg --verify file.sig file

# Key servers (find public keys)
gpg --keyserver keys.openpgp.org --search their@email.com
```

---

## Password Management

### Tools

| Tool | Type | Encryption | Notes |
|------|------|-----------|-------|
| **KeePassXC** | Local file | AES-256 + Argon2id | Offline, cross-platform, best for advanced users |
| **Bitwarden** | Cloud | AES-256 | Open-source, free, audited |
| **Vaultwarden** | Self-hosted | AES-256 | Self-hosted Bitwarden — full control |
| **Pass** | Local/Git | GPG | CLI-based, Unix philosophy |

### KeePassXC Full Hardening

```
Settings → Security:
  Database lock after: 5 minutes
  Clear clipboard after: 30 seconds
  Minimize on database lock: ON
  Lock when minimized: ON
  Re-lock after global auto-type: ON

Database → Encryption Settings:
  Algorithm: AES-256
  KDF: Argon2id
  Memory: 128 MB
  Iterations: 16
  Parallelism: 2

Credentials:
  Password + Key File (store key file on separate USB drive)
  OR Password + YubiKey HMAC-SHA1 challenge-response
```

### Pass — Unix Password Manager

```bash
# Install
sudo apt install pass

# Initialize with your PGP key
pass init YOUR_GPG_KEY_ID

# Add a password
pass insert github.com/username

# Generate a random password
pass generate github.com/username 32

# Retrieve
pass github.com/username

# Git sync (encrypted — safe to use with any Git host)
pass git init
pass git remote add origin git@github.com:yourrepo/password-store.git
pass git push
```

---

## Two-Factor Authentication

| Method | Phishing Resistant | Interception Resistant | Notes |
|--------|------------------|----------------------|-------|
| **FIDO2 Hardware Key** (YubiKey) | ✅✅ | ✅✅ | Best — domain-bound |
| **Passkey** | ✅✅ | ✅✅ | Device-bound FIDO2 |
| **TOTP** (Aegis, KeePassXC) | ✅ | ✅ | Good, sync offline |
| **HOTP** | ✅ | ✅ | Counter-based TOTP |
| **Email OTP** | ⚠️ | ⚠️ | As secure as your email |
| **SMS** | ❌ | ❌ | SIM swap, SS7 attacks |

### YubiKey Setup

```bash
# Install tools
sudo apt install yubikey-manager yubikey-personalization libpam-u2f

# List connected key
ykman list
ykman info

# Set FIDO2 PIN (required for FIDO2 usage)
ykman fido access change-pin

# Register for SSH
ssh-keygen -t ed25519-sk  # Requires YubiKey touch

# PAM integration — require YubiKey for sudo
pamu2fcfg > ~/.config/Yubico/u2f_keys
# /etc/pam.d/sudo:
# auth required pam_u2f.so authfile=/home/user/.config/Yubico/u2f_keys
```

---

## File Encryption

### GPG Symmetric Encryption

```bash
# Encrypt a file with passphrase (no key required)
gpg --symmetric --armor --cipher-algo AES256 secret.txt

# Decrypt
gpg --decrypt secret.txt.asc
```

### age — Modern File Encryption

```bash
# Install
sudo apt install age

# Generate key
age-keygen -o key.txt

# Encrypt
age -r $(age-keygen -y key.txt) secret.txt > secret.txt.age

# Decrypt
age -d -i key.txt secret.txt.age > secret.txt

# Encrypt to multiple recipients (share with team)
age -r <recipient1_pubkey> -r <recipient2_pubkey> file.txt > file.txt.age
```

### VeraCrypt Hidden Volumes

```bash
# Outer volume: innocuous data (photos, music) — simple password
# Inner hidden volume: sensitive data — different password
# If coerced: reveal outer password
# Inner volume is mathematically indistinguishable from random data

# Create via GUI or CLI
veracrypt --create /path/to/file --volume-type=hidden
```

---

## Identity & Profile Separation

### Compartmentalization Model

```
Identity A: Real Name (personal social media, shopping, email)
Identity B: Work (professional email, LinkedIn, work tools)
Identity C: Pseudonymous (forums, Discord, GitHub hobby projects)
Identity D: Anonymous (Tor only, no persistent accounts, Tails)

Rules:
- NEVER cross-contaminate these identities
- Different browsers, different VPN exits, different email addresses
- Different writing styles (stylometry) if high-risk
- Different time zones if needed
```

### Browser Profile + Container Setup

```bash
# Firefox profiles
firefox -P "Personal" --no-remote
firefox -P "Work" --no-remote
firefox -P "Anonymous" --no-remote  # Always Tor or Mullvad Browser

# Containers in Firefox Multi-Account Containers:
# Personal   — banking, shopping
# Work       — work tools, LinkedIn
# Social     — Discord, Reddit, Twitter
# Dangerous  — sketchy sites (isolated)
```

---

## Virtualization & Sandboxing

### KVM/QEMU

```bash
sudo apt install qemu-kvm libvirt-daemon-system virt-manager

# Create VM
virt-manager  # GUI

# Or CLI
virt-install \
  --name "untrusted" \
  --memory 4096 \
  --vcpus 2 \
  --disk path=/var/lib/libvirt/images/untrusted.qcow2,size=50 \
  --os-variant ubuntu22.04 \
  --cdrom /path/to/ubuntu.iso \
  --network network=default
```

### Firejail — Application Sandbox

```bash
sudo apt install firejail

# Sandbox with private home
firejail --private firefox

# Sandbox with no network
firejail --net=none libreoffice suspicious.docx

# Sandbox with custom profile
firejail --profile=/etc/firejail/firefox.profile firefox

# List sandboxed processes
firejail --list
```

### Bubblewrap — Lightweight Sandbox

```bash
# Install
sudo apt install bubblewrap

# Run application with restricted access
bwrap --ro-bind /usr /usr --ro-bind /lib /lib \
  --bind /tmp /tmp \
  --unshare-net \
  --die-with-parent \
  /usr/bin/application
```

---

## Metadata Hygiene

### Images

```bash
# View all metadata
exiftool photo.jpg

# Check for GPS specifically
exiftool photo.jpg | grep -iE "gps|location|latitude|longitude"

# Strip all metadata
exiftool -all= photo.jpg

# Strip recursively
exiftool -all= -r ./photos/

# mat2 (strips even more aggressively)
sudo apt install mat2
mat2 photo.jpg
mat2 --inplace *.jpg
```

### Documents

```bash
# PDF
mat2 document.pdf
exiftool -all= document.pdf
pdftk document.pdf output cleaned.pdf

# DOCX / ODS / PPTX
mat2 document.docx

# LibreOffice: File → Properties → Reset Properties

# Image embedded in document (can contain GPS/metadata)
# Always flatten + re-export
```

### Audio / Video

```bash
# Strip audio tags
ffmpeg -i input.mp3 -map_metadata -1 -c:a copy output.mp3

# Strip video metadata
ffmpeg -i input.mp4 -map_metadata -1 -c copy output.mp4

# Verify
ffprobe -v quiet -print_format json -show_format output.mp4 | grep -i "tag\|meta"
```

---

## Secure Deletion

### shred — Overwrite Before Delete

```bash
# Overwrite with random data (3 passes) then delete
shred -uvz -n 3 sensitive_file.txt

# Overwrite entire drive (before disposal)
shred -vz -n 3 /dev/sdX

# srm — secure-remove (alternative)
sudo apt install secure-delete
srm -z sensitive_file.txt
```

### SSD Note

`shred` is **less reliable on SSDs** due to wear leveling — the drive may remap sectors.

```bash
# For SSDs: use ATA Secure Erase
sudo hdparm -I /dev/sdX | grep "supported"
sudo hdparm --security-set-pass PASSWORD /dev/sdX
sudo hdparm --security-erase PASSWORD /dev/sdX

# Or encrypt the full drive first, then wipe the key
# Cryptsetup erase — destroys key material, making data unrecoverable
cryptsetup erase /dev/sdX
```

---

## Secure Communication

| Tool | E2EE | Metadata | Phone Nr | Notes |
|------|------|---------|---------|-------|
| **Signal** | ✅ | Minimal | Required | Best for trusted contacts |
| **Session** | ✅ | None | None | Decentralized, no metadata |
| **SimpleX** | ✅ | None | None | No user IDs at all |
| **Briar** | ✅ | None | None | P2P, works over Bluetooth/WiFi/Tor |
| **Matrix** | ✅ (E2EE rooms) | Variable | Optional | Self-hostable |
| **XMPP + OMEMO** | ✅ | Variable | Optional | Federated, self-hostable |
| **Cwtch** | ✅ | None | None | Tor-based, metadata resistant |

---

## Network Monitoring

```bash
# Real-time connections
watch -n 1 "ss -tulnp"
nethogs    # Per-process bandwidth
iftop      # Per-connection bandwidth

# Capture and analyze traffic
sudo tcpdump -i eth0 -n -w capture.pcap
wireshark capture.pcap  # Analyze in GUI

# Monitor all outbound DNS (should be zero if DoH is working)
sudo tcpdump -i any -n port 53

# See who your applications are talking to
sudo netstat -tulnp
sudo lsof -i -P -n

# Full traffic analysis
sudo apt install vnstat
vnstat --live
```

---

## System Audit & Hardening

### Lynis — Full System Audit

```bash
sudo apt install lynis
sudo lynis audit system
# Read /var/log/lynis.log for full details
# Aim for hardening index > 80
```

### Rootkit Detection

```bash
sudo apt install rkhunter chkrootkit

sudo rkhunter --update
sudo rkhunter --propupd  # Save current state
sudo rkhunter --check

sudo chkrootkit
```

### fail2ban — Brute Force Protection

```bash
sudo apt install fail2ban

# /etc/fail2ban/jail.local
[DEFAULT]
bantime  = 3600
findtime = 600
maxretry = 3
backend = systemd

[sshd]
enabled = true
port = ssh
logpath = %(sshd_log)s

sudo systemctl enable --now fail2ban
fail2ban-client status sshd
```

---

## Kernel Hardening

```bash
# /etc/sysctl.d/99-harden.conf

# IP hardening
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1
net.ipv4.icmp_echo_ignore_broadcasts = 1
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv4.conf.all.send_redirects = 0
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_timestamps = 0

# IPv6
net.ipv6.conf.all.accept_redirects = 0
net.ipv6.conf.default.accept_redirects = 0

# Kernel memory
kernel.randomize_va_space = 2       # Full ASLR
kernel.kptr_restrict = 2            # Hide kernel pointers
kernel.dmesg_restrict = 1           # Restrict dmesg
kernel.perf_event_paranoid = 3      # Restrict perf

# Filesystem
fs.protected_hardlinks = 1
fs.protected_symlinks = 1
fs.suid_dumpable = 0

# Process tracing
kernel.yama.ptrace_scope = 2        # Restrict ptrace

sudo sysctl -p /etc/sysctl.d/99-harden.conf
```

### Hardened Kernel

```bash
# Arch
sudo pacman -S linux-hardened

# Void
sudo xbps-install linux-hardened

# Fedora (custom build or copr)
# Hardened kernel includes:
# - Stronger ASLR
# - More aggressive randomization
# - Additional exploit mitigations
# - Reduced attack surface via module disabling
```

---

## AppArmor & SELinux

### AppArmor (Ubuntu/Debian)

```bash
sudo apt install apparmor apparmor-utils

# Check status
sudo aa-status

# Put a profile in enforce mode
sudo aa-enforce /etc/apparmor.d/usr.bin.firefox

# Put a profile in complain mode (log violations, don't block)
sudo aa-complain /etc/apparmor.d/usr.bin.firefox

# Generate a new profile
sudo aa-genprof /usr/bin/application

# Check AppArmor logs
sudo dmesg | grep -i apparmor
```

### SELinux (Fedora/RHEL)

```bash
# Check status
getenforce   # Should be "Enforcing"
sestatus

# Set to enforcing
sudo setenforce 1
# /etc/selinux/config → SELINUX=enforcing

# View denials
sudo sealert -a /var/log/audit/audit.log
sudo ausearch -m avc -ts recent
```

---

## Common Failure Points

| Failure | Fix |
|---------|-----|
| Reusing usernames | Unique names per identity, no exceptions |
| Mixing identities in one browser session | Containers or separate browsers |
| Logging into personal accounts anonymously | Hard separation — different device/VM |
| Cloud sync of sensitive files | Cryptomator, Nextcloud self-hosted, or local only |
| Unencrypted disk | Enable LUKS at install time |
| SMS 2FA | Switch to TOTP (Aegis) or YubiKey |
| Browser extensions with "access to all sites" | Minimize — every extension = fingerprint increase |
| Sharing files without stripping metadata | mat2 / exiftool before every share |
| Using the same email for signups | SimpleLogin / AnonAddy aliases |
| VPN without kill switch | Any VPN drop = IP leak |
| Skipping updates | Security patches exist because vulnerabilities are real |
| Thinking VPN = anonymity | VPN hides from ISP, not from behavior tracking |
| Tor for everything | Tor is slow — use it where anonymity matters |
| Ignoring DNS leaks | Test with dnsleaktest.com on every new config |

---

## Quick Reference

```
Anonymity OS  →  Tails (live USB) · Whonix (VM) · Qubes (full system)
Privacy Linux →  Fedora · Debian · Arch · Void · NixOS
Extreme       →  OpenBSD · Alpine · HardenedBSD
VPN           →  WireGuard · Kill switch · Mullvad / ProtonVPN / IVPN
Tor           →  Browser: Tor Browser / Mullvad · System: torrc + iptables
DNS           →  DoH/DoT · Mullvad or NextDNS · No plain port 53
Firewall      →  nftables · default deny · block all non-VPN outbound
Disk          →  LUKS2 (Argon2id) · VeraCrypt (containers) · Cryptomator (cloud)
Browser       →  Tor Browser > Mullvad Browser > LibreWolf > Brave
Extensions    →  uBlock Origin · ClearURLs · Containers · that's it
Email         →  ProtonMail/Tutanota · SimpleLogin aliases · PGP where possible
Passwords     →  KeePassXC · YubiKey 2FA · Unique everywhere · 20+ chars
Files         →  age / GPG encrypt · mat2 / exiftool strip · shred delete
Comms         →  Signal (trusted) · Session / SimpleX (anon) · Briar (P2P)
Firmware      →  Coreboot/Libreboot · Disable ME · BIOS password + Secure Boot
Kernel        →  linux-hardened · sysctl hardening · ASLR=2
```

---

<div align="center">

> **Privacy is not hiding.**
> It's reducing the surface area of what can be linked, tracked, and sold.

<img width="100%" src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=2,3,6&height=100&section=footer"/>

**[↑ Top](#-device-hardening-guide)** · [📱 Phone Hardening](https://github.com/woodbloom/Phone-hardening-guide) · [🖥️ Server Hardening](https://github.com/woodbloom/Server-hardening-guide)

</div>
