# Findings — IoT / SSH-Telnet Honeypot (Sep–Nov 2026, ~11 weeks)

A multi-sensor **Cowrie** honeynet (SSH + Telnet) left exposed to live internet traffic.
This document summarises what was observed. It contains **no operator, victim, or
infrastructure data, and no secrets** — attacker indicators are described by family and
behaviour (full IOCs were contributed to ThreatFox/AbuseIPDB, not published here).

## Scale

| Metric | Count |
|---|---|
| Sessions | 318,000+ |
| Login attempts | 377,000+ |
| Commands captured | 400,000+ |
| Unique attacker IPs | 8,200+ |
| Unique downloaded samples | ~200 |

## Attack families observed

| Family | Signature (observed) | Behaviour | Detection signal |
|---|---|---|---|
| **Mirai & variants** (dominant) | `busybox` probes, `dvrHelper`, multi-arch ELF drops | Hijacks IoT devices into a DDoS botnet | busybox running unexpected commands; same IP dropping ARM + MIPS + x86 |
| **Gafgyt / BASHLITE** | `GAYFGT` marker string | IoT DDoS botnet | `GAYFGT` signature |
| **Outlaw / Shellbot** | rewrites SSH keys; `mdrfckr` marker | SSH key backdoor + miner + IRC bot | unauthorised write to the SSH keys file |
| **Coin miners (XMRig)** | stratum/miner strings | Steals CPU to mine Monero | outbound stratum; high CPU; known wallet |
| **Cloud-credential stealer** (newer) | scans `.env` / `config.*` for `amazonses` / SMTP keys | Steals AWS SES / SMTP / cloud secrets | bulk reads of env/config files on a server |

## Credential targeting

Attackers overwhelmingly sprayed **IoT factory-default credentials** — strong evidence of
mass hunting for DVRs, IP cameras and routers:

- `xc3511` (XiongMai), `vizxv` / `888888` / `7ujMko0admin` (Dahua), `Zte521` (ZTE),
  `juantech`, `hi3518` (HiSilicon camera SoC), `54321`, `anko`.
- Most-tried pairs: `root/root`, `root/xc3511`, `root/vizxv`, `admin/admin`,
  `345gs5662d34/345gs5662d34` (a known IoT-scanner signature).

The full, de-duplicated credential lists are in [`passwords.txt`](passwords.txt) and
[`usernames.txt`](usernames.txt).

## Malware profile (~200 unique samples, static analysis only)

- **Families:** Mirai-heavy, plus coin miners, Gafgyt, Tsunami/Kaiten.
- **Architectures:** ARM, x86-64, MIPS, i386, PowerPC — classic multi-architecture IoT
  botnet spread (drop whatever matches the victim CPU).
- Also captured: shell-script droppers and an attacker's own SSH key uploaded during a
  copy-based delivery chain.

> Samples were never executed — analysis was static (`file` + `strings` + family
> signatures) in an isolated environment. Raw malware is **not** published here.

## A note on sharing honeypot data safely

When open-sourcing the wordlist, GitHub's secret-scanning push protection flagged a **real
leaked access token** that an attacker had sprayed as a *password*. It was removed before
publishing. **Always scrub honeypot credential data for real secrets before sharing it.**

## Defensive takeaways

1. **Change default passwords** — factory IoT credentials are the #1 entry vector.
2. **SSH: key-only, no passwords**, and move off port 22 where possible.
3. **Isolate IoT devices** on a separate network/VLAN.
4. **Watch for env/config harvesting** — a server bulk-reading `.env`/`config` files is a
   strong compromise signal.
5. The traffic is **automated and constant** — assume exposure means attempted compromise
   within minutes.

---

*Indicators from this honeypot were contributed to [ThreatFox](https://threatfox.abuse.ch/)
and [AbuseIPDB](https://www.abuseipdb.com/) for the defender community. CC0 — free to use.*
