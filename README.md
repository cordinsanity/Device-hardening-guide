# Device Hardening Guide
A practical guide to reducing digital linkability through device hardening, network hygiene, and identity separation.

________________________________________________________________________________________________________________________

## VPN Configuration

- VPN always enabled before internet use
- Kill switch enabled at all times
- WireGuard protocol preferred
- Full-tunnel mode only (no split tunneling)
- Auto-connect on system startup
- Verify DNS leak protection is active

### Limitations

A VPN does NOT protect against:

- browser fingerprinting
- logged-in accounts (Google, social media, etc.)
- behavioral tracking
- malware or compromised devices

________________________________________________________________________________________________________________________

## Network Hygiene

- Do not mix different identities in the same session
- Separate browsing contexts for different purposes
- Avoid logging into personal accounts in isolated workflows
- Restart browser sessions or profiles when switching contexts

________________________________________________________________________________________________________________________

## Device Hardening

### Primary System

- Linux (Debian / Fedora / Linux Mint)
- Full Disk Encryption (LUKS)
- Minimal software installation
- Regular system updates

### Advanced Option

- Qubes OS for strict isolation (advanced users only)

### Practical Rule

Avoid excessive fragmentation across too many devices. Complexity increases mistakes.

________________________________________________________________________________________________________________________

## Browser Hardening (Firefox-based only)

### Recommended Browsers

- Firefox ESR
- LibreWolf
- Mullvad Browser

### Baseline Settings

- Disable telemetry
- Enable HTTPS-only mode
- Disable WebRTC (or restrict leaks)
- Disable Pocket and sponsored content
- Enable strict tracking protection

________________________________________________________________________________________________________________________

## Profile Separation

Use separate browser profiles for:

- personal use
- work
- finance
- anonymous browsing

### Rule

Never mix identities within a single browser profile.

________________________________________________________________________________________________________________________

## Extensions (Minimal Set)

- uBlock Origin
- ClearURLs
- Cookie AutoDelete
- Temporary Containers (Firefox only)

### Rule

Fewer extensions reduce fingerprint surface.

________________________________________________________________________________________________________________________

## Metadata Hygiene

- Remove EXIF data from images
- Strip document metadata before sharing
- Avoid cloud auto-sync for sensitive files
- Prefer offline tools for sensitive work

________________________________________________________________________________________________________________________

## Communication Hygiene

- Use Signal / Session / SimpleX depending on use case
- Do not sync contacts automatically
- Do not reuse identifiers across contexts
- Keep accounts strictly separated

________________________________________________________________________________________________________________________

## Common Failure Points

Most privacy failures come from behavior:

- reusing usernames
- mixing accounts
- logging into personal services in separate contexts
- cloud backups
- predictable usage patterns

________________________________________________________________________________________________________________________

## Final

Privacy is not hiding.  
It is reducing linkability and limiting data exposure.
