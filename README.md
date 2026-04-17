# UniFi Backup Explorer v2.1

A client-side tool for decrypting and exploring UniFi Network Application backup files (.unf). No installation, no server, no data leaves your machine.

## How to use

1. Download `unifi_backup_explorer_v2.html`
2. Open it in any modern browser
3. Drag your `.unf` backup file onto the page

## What you'll see

Your backup parsed into categorised collections — firewall rules, firewall zones, VLANs, WiFi SSIDs, WireGuard peers, traffic routes, devices, clients, schedules, and controller settings.

## Security model

- Decryption runs entirely in the browser using the Web Crypto API
- Read-only — the tool never writes back to the backup file
- No network requests are made during decryption or parsing
- CDN dependencies locked with SRI integrity hashes
- Content Security Policy applied to limit attack surface
- Nothing is stored, logged, or transmitted

## Export warning

Exported JSON contains credentials in cleartext — WiFi passphrases, WireGuard keys, PPPoE passwords, RADIUS secrets. Treat exported files with the same care as the backup itself.

## Notes

The encryption key used by UniFi backups is a publicly documented constant. This tool does not bypass any security — it uses the same key the controller uses.

If this page is ever served over HTTP rather than opened as a local file, set `Content-Security-Policy` and `X-Frame-Options: DENY` as server response headers.

## Author

Chris Mallia
