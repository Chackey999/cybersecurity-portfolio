# Systems as Attack Vectors — Supply Chain & Vulnerability Risk

**Lab context:** TryHackMe SOC Level 1 path — module on system-level (as opposed to human-level) attack surfaces.
**Type:** Coursework / conceptual lab

## What this module covered

How threat actors target infrastructure directly rather than tricking a user: exploiting unpatched vulnerabilities, or compromising a shared dependency (a "supply chain attack") to reach many victims through one trusted update channel.

## Key takeaways (in my own words)

- **Blast radius matters.** Compromising one employee's mailbox affects one person; compromising the mail *server* affects every mailbox on it. System-level attacks scale in a way human-level attacks usually don't.
- **Supply chain attacks are hard to fully prevent** because an organization rarely controls every library or vendor dependency running inside its own software stack. The SolarWinds and 3CX incidents are well-known examples of this — a single compromised update reached thousands of downstream organizations.
- Over 40,000 CVEs were published in a recent year, with only a few hundred actively exploited in the wild — this reinforced for me why **vulnerability prioritization** (not just patching everything) is a core SOC/vuln management skill.
- The majority of breaches still trace back to **stolen or reused passwords** — a reminder that "system" risk and "human" risk are rarely fully separable in practice.

## Why this matters for my transition

This module connected directly to independent research I did afterward on the 2025 SharePoint "ToolShell" vulnerability chain (see [`05-toolshell-cve-analysis`](../05-toolshell-cve-analysis)) — a real-world, large-scale example of exactly the system-level exploitation this module describes.
