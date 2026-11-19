# IoT / SSH-Telnet Honeypot Credential Wordlist

## Findings & methodology

Attack families, credential targeting, malware profile and defensive takeaways: [FINDINGS.md](FINDINGS.md)


Real username & password guesses captured by a **Cowrie honeynet** (SSH + Telnet),
deduplicated and frequency-ranked.

- **Collection window:** 2026-09-02 – 2026-11-19 (~11 hafta / 2.5 ay)
- **Unique passwords:** 39,893  (`passwords.txt`)
- **Unique usernames:** 5,308  (`usernames.txt`)
- **Source:** live internet attack traffic against IoT-style decoys
- **Top credentials observed:** root/root, root/xc3511, root/vizxv, admin/admin,
  345gs5662d34/345gs5662d34 — heavy targeting of IoT factory defaults
  (XiongMai `xc3511`, Dahua `vizxv`/`888888`, ZTE `Zte521`, HiSilicon `hi3518`).

## Use
Defensive only: weak-credential auditing, detection rules, honeypot research.
No infrastructure, operator, or victim data is included.

## License
Public domain / CC0 — free to use, no attribution required.