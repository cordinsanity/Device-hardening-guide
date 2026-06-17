# Device Hardening Guide
> A practical guide to reducing digital linkability through device hardening, network hygiene, and identity separation.

**Threat model:** Reduce passive tracking, advertising profiling, and behavioral linkability. Not designed for nation-state adversaries.

---

## Table of Contents

- [VPN](#vpn)
- [DNS](#dns)
- [Network Hygiene](#network-hygiene)
- [Operating System](#operating-system)
- [Browser Hardening](#browser-hardening)
- [Extensions](#extensions)
- [Profile & Identity Separation](#profile--identity-separation)
- [Metadata Hygiene](#metadata-hygiene)
- [Communication](#communication)
- [Common Failure Points](#common-failure-points)

---

## VPN

### Configuration
- Enable kill switch — block all traffic if VPN drops
- Use **WireGuard** protocol (faster, smaller attack surface than OpenVPN)
- Full-tunnel only — no split tunneling
- Auto-connect on system startup
- Verify DNS leak protection is active (`dnsleaktest.com`)

### Provider selection
- No-log policy with audits (Mullvad, ProtonVPN, IVPN)
- Jurisdiction outside 5/9/14 Eyes preferred
- Accepts cash or crypto payment

### What a VPN does NOT protect against
| Threat | Protected? |
|--------|-----------|
| Browser fingerprinting | ❌ No |
| Logged-in accounts (Google, Meta, etc.) | ❌ No |
| Behavioral tracking | ❌ No |
| Malware | ❌ No |
| IP-based geolocation | ✅ Yes |
| ISP traffic monitoring | ✅ Yes |

---

## DNS

- Use **DNS-over-HTTPS (DoH)** or **DNS-over-TLS (DoT)**
- Recommended resolvers: Mullvad, NextDNS, Cloudflare (1.1.1.1)
- Block DNS at system level, not just browser

### Linux (systemd-resolved)
```bash
# /etc/systemd/resolved.conf
[Resolve]
DNS=1.1.1.1#cloudflare-dns.com 1.0.0.1#cloudflare-dns.com
DNSOverTLS=yes
```

### Verify
```bash
resolvectl status
```

---

## Network Hygiene

- Never mix identities in the same browser session
- Use separate browser profiles or containers per context
- Avoid logging into personal accounts in anonymous workflows
- Restart sessions or clear state when switching contexts
- Use a separate device or VM for sensitive work when possible

---

## Operating System

### Recommended
| OS | Use case |
|----|----------|
| **Fedora / Debian** | Daily driver, good update cadence |
| **Linux Mint** | Beginner-friendly, stable |
| **Qubes OS** | Maximum isolation (advanced) |
| **Tails** | Amnesic, live USB, Tor-only |

### Hardening checklist
- [x] Full disk encryption (LUKS on Linux)
- [x] Automatic screen lock (< 5 minutes)
- [x] Firewall enabled (`ufw` or `firewalld`)
- [x] Automatic security updates
- [x] Minimal software installation
- [x] No unnecessary services running
- [x] BIOS/UEFI password set
- [x] Secure Boot enabled where possible

### Firewall (ufw)
```bash
ufw default deny incoming
ufw default allow outgoing
ufw enable
```

### Check open ports
```bash
ss -tulnp
```

---

## Browser Hardening

### Recommended browsers (in order)
1. **Mullvad Browser** — hardened Firefox, Tor Browser network without Tor, anti-fingerprinting by default
2. **LibreWolf** — hardened Firefox fork, no telemetry
3. **Firefox ESR** — requires manual hardening

### Baseline settings (Firefox / LibreWolf)
- Disable telemetry: `about:telemetry` → all off
- Enable HTTPS-only mode
- Disable WebRTC or restrict leaks (`media.peerconnection.enabled = false`)
- Disable Pocket, sponsored shortcuts, Firefox account sync
- Strict Enhanced Tracking Protection

### about:config tweaks
```
privacy.resistFingerprinting = true
privacy.firstparty.isolate = true
geo.enabled = false
media.navigator.enabled = false
network.http.referer.XOriginPolicy = 2
dom.battery.enabled = false
```

### Fingerprinting
Your browser fingerprint can identify you even without cookies. Test at:
- `coveryourtracks.eff.org`
- `browserleaks.com`

Using **Mullvad Browser** or **Tor Browser** provides the best fingerprinting protection by making all users look identical.

---

## Extensions

**Minimal set — fewer extensions = smaller fingerprint surface**

| Extension | Purpose |
|-----------|---------|
| **uBlock Origin** | Ad/tracker blocking (use medium or hard mode) |
| **ClearURLs** | Strip tracking parameters from URLs |
| **Cookie AutoDelete** | Auto-delete cookies on tab close |
| **Temporary Containers** (Firefox) | Isolate each site in its own container |

### Do NOT install
- Password managers as browser extensions (use standalone)
- Any extension requesting access to all sites unless absolutely necessary
- Multiple redundant blockers

---

## Profile & Identity Separation

### Rule: one identity per context

| Context | Separate? |
|---------|----------|
| Personal browsing | ✅ Dedicated profile |
| Work | ✅ Dedicated profile |
| Finance | ✅ Dedicated profile |
| Anonymous / research | ✅ Dedicated browser or VM |

### Implementation options
- Firefox Multi-Account Containers
- Separate browser profiles (`firefox -P`)
- Separate VMs (Qubes OS, VirtualBox)
- Separate physical devices

### Never
- Log into personal accounts in anonymous sessions
- Reuse usernames across contexts
- Access personal email in a work or anonymous context

---

## Metadata Hygiene

### Images
- Strip EXIF before sharing: `exiftool -all= file.jpg`
- Check: `exiftool file.jpg | grep -i gps`

### Documents
- Strip metadata: LibreOffice → File → Properties → Remove personal info
- Or: `mat2 document.pdf`

### Rules
- Avoid cloud auto-sync for sensitive files (iCloud, Google Drive, OneDrive)
- Prefer offline tools for sensitive work
- Re-encoding images can reintroduce metadata depending on the app

---

## Communication

| Tool | Use case | Notes |
|------|----------|-------|
| **Signal** | Trusted contacts | Enable disappearing messages |
| **Session** | No phone number required | Decentralized, no metadata |
| **SimpleX** | Maximum metadata protection | No user IDs at all |
| **Matrix/Element** | Self-hosted option | Requires trusted server |

### Rules
- Do not sync contacts automatically
- Do not reuse phone numbers or email addresses across identities
- Keep accounts strictly separated per context

---

## Common Failure Points

Most privacy failures are **behavioral**, not technical:

| Failure | Fix |
|---------|-----|
| Reusing usernames | Use unique names per context |
| Mixing accounts in one session | Use containers or separate profiles |
| Cloud backup of sensitive data | Use local or encrypted backup only |
| Logging into personal accounts while "anonymous" | Hard separation only |
| Predictable usage patterns | Vary behavior, use scheduled VPN |
| Browser extensions with broad permissions | Minimize extension count |
| Ignoring software updates | Enable automatic security updates |

---

## Quick Reference

```
VPN        →  WireGuard · Kill switch · No logs · DoH verified
Browser    →  Mullvad Browser or LibreWolf · resistFingerprinting
Extensions →  uBlock Origin · ClearURLs · Temporary Containers
Identity   →  One context per profile · Never mix
Disk       →  LUKS encryption · Firewall · Minimal install
Comms      →  Signal / Session / SimpleX
Metadata   →  exiftool / mat2 before sharing
```

---

> Privacy is not hiding.  
> It is reducing linkability and limiting the surface area of what can be tracked.