# OSINT & Vulnerability Research Skills

**Lab context:** TryHackMe modules on open-source intelligence gathering and vulnerability/threat lookups.
**Type:** Coursework / guided lab exercises

## Tools and skills practiced

- **CVE Databases** (e.g., NVD) — searching for known vulnerabilities by software name/version (e.g., looking up Apache version banners against published CVEs) to assess exposure
- **VirusTotal** — checking file/URL reputation across multiple security vendors as part of triage
- **Shodan/Censys-style service search concepts** — recognizing how exposed services and version banners can be used defensively (to find your own exposure) or offensively (by attackers)
- **GitHub reconnaissance** — understanding how public repositories can inadvertently leak information useful to attackers (credentials, internal hostnames, config details), and why organizations need policies around this
- **Linux man pages / technical documentation** — using `man` and reference docs to understand tool behavior precisely rather than guessing (e.g., confirming UDP connection syntax with `nc -u host port`)

## Why this matters

OSINT and vulnerability lookup are foundational SOC L1 skills — before you can triage an alert, you often need to quickly answer "is this software version actually vulnerable, and has this indicator been seen before?" These exercises built the habit of checking primary sources (CVE records, vendor advisories) rather than assuming.

This complements my independent research write-up on CVE-2025-53770/53771 (ToolShell) in [`05-toolshell-cve-analysis`](../05-toolshell-cve-analysis), where I applied the same CVE-lookup and source-verification approach to a real, current vulnerability.
