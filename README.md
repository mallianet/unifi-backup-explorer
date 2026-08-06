# UniFi Backup Explorer

Browser-based decryptor and explorer for UniFi Network Application backup files (`.unf`). No installation. No terminal. No data leaves your machine.

**Try it:** [mallianet.github.io/unifi-backup-explorer](https://mallianet.github.io/unifi-backup-explorer/)

The hosted page is the same single `index.html` from this repository — nothing is uploaded to it. Your `.unf` file is read and decrypted locally in your browser; the backup never leaves your machine. If you would rather not trust the hosted copy, download the file and open it from disk instead.

![UniFi Backup Explorer UI](docs/screenshot.png)

## Why this exists

The existing tools for decrypting UniFi `.unf` backups are command-line only — they need OpenSSL, `mongo-tools`, `bsondump`, and a working bash environment just to look at what's in a backup. That's a high bar for anyone who just wants to audit their own network configuration.

A backup is the most complete record of how a site is configured, but on UniFi it is an opaque encrypted blob. There is no equivalent of opening `config.xml` on pfSense or OPNsense, or diffing a Cisco `running-config` export. That gap is what this fills: producing a readable configuration report for an auditor, analysing a backup offline without restoring it, checking a single setting without a full restore, and comparing how two sites are configured.

This tool does everything in the browser. Drag the `.unf` file onto the page, read your configuration.

## Features

- **One-click audit report** — a print-ready summary of the whole site: networks and VLANs, firewall zones, user-defined firewall policies in evaluation order, WiFi SSIDs, WireGuard peers by name, and a device inventory with firmware versions. Print it or save it as a PDF from the browser's print dialog. Credentials are excluded by design, and the exclusion is verified by an automated leak check before the report opens.
- **Single-file HTML** — no installation, no local package managers, no terminal. Loads JSZip and pako from cdnjs at runtime (locked with SRI hashes).
- **Fully client-side** — decryption happens in your browser via the Web Crypto API
- **17 categorised collections** — firewall groups, rules, policies, zones, traffic routes, networks/VLANs, WireGuard peers, WiFi SSIDs, devices, users, user groups, privileges, scheduled tasks, schema, settings, tombstones, uncategorised
- **Grouped navigation** — Security / Network / Devices / System / Other
- **Live filter** — search across collections and documents
- **Credential-aware** — warns on export that JSON contains plaintext WiFi passphrases, WireGuard keys, PPPoE passwords, and RADIUS secrets
- **UniFi Network 10.x aware** — tested against current backup format

## How to use

Either open the [hosted page](https://mallianet.github.io/unifi-backup-explorer/), or run it locally:

1. Download `index.html` from this repository
2. Open it in any modern browser (Chrome, Firefox, Safari, Edge)
3. Drag your `.unf` backup file onto the page
4. Browse the parsed collections and export what you need

The tool was previously named `unifi_backup_explorer_v2.html`. That filename still resolves — it is now a small page that redirects to `index.html`, so existing links keep working.

## Security model

- Decryption runs entirely in the browser using the Web Crypto API
- Read-only — the tool never writes back to the backup file
- No network requests are made after page load — decryption, parsing, and rendering are all local
- CDN dependencies locked with Subresource Integrity (SRI) hashes
- Content Security Policy applied to limit attack surface
- Nothing is stored, logged, or transmitted

The encryption key used by UniFi backups is a publicly documented constant. This tool does not bypass any security — it uses the same key the controller uses.

## Audit history

The code has been through multiple security review passes:

- Initial Opus red team and Gemini passes before first publication
- Claude Code `/review` and `/security-review` on Sonnet 4.6
- Claude Code `/ultrareview` on Opus 4.7

Findings addressed include XSS escaping, event delegation, BSON parser hardening (bounds checking, recursion limits, prototype pollution protection), credential export warnings, CSP improvements, and SRI integrity hashes. See the commit history for details.

## Export warning

Exported JSON contains credentials in cleartext — WiFi passphrases, WireGuard keys, PPPoE passwords, RADIUS secrets. Treat exported files with the same care as the backup itself.

## Hosting note

The page carries its own Content-Security-Policy as a `<meta http-equiv>` tag, which is what applies when the file is opened from disk or served from GitHub Pages — Pages does not allow custom response headers, so `Content-Security-Policy` and `X-Frame-Options` cannot be set there. The meta-tag CSP includes `frame-ancestors 'none'`, though note that `frame-ancestors` is ignored when delivered via meta tag rather than a response header.

If you self-host this page on a server you control, set `Content-Security-Policy` and `X-Frame-Options: DENY` as real response headers. Header-delivered policy supersedes the meta tag and is the only way to get working clickjacking protection.

## License

MIT — see [LICENSE](LICENSE).

## Author

Chris Mallia — [CM Networks](https://mallianet.com)
