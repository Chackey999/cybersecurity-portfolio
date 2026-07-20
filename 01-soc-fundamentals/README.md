# SOC Structure & Blue Team Career Path

**Lab context:** TryHackMe SOC Level 1 path — introductory module on security team structure.
**Type:** Coursework / conceptual lab (no live systems involved)

## What this module covered

An overview of how larger organizations structure their security function: a CISO overseeing separate Red Team (offensive), GRC (compliance/policy), and Blue Team (defensive) functions, with the SOC sitting inside Blue Team as the first line of defense.

## Key takeaways (in my own words)

- **SOC L1 analysts triage alerts** and escalate anything beyond their scope to L2 — this is the natural entry point into a defensive security career, which is exactly why I'm targeting L1 roles.
- **CIRT/CERT teams** step in when an incident exceeds normal SOC capacity — they need broad, tool-agnostic incident response knowledge rather than deep familiarity with one SIEM/EDR platform.
- Organizations without in-house SOC capability often use an **MSSP (Managed Security Service Provider)**. MSSP roles tend to be higher-pressure but are a realistic and common way to break into the field.
- Career progression from L1 typically branches into L2 analyst, SOC engineering, CIRT, or management — the direction usually reveals itself once you're doing the L1 work day to day.

## Practical exercise

The module included a scenario-matching exercise: given seven simultaneous security incidents (e.g., a firewall brute-force alert, a ransomware outbreak, a PCI DSS audit request, an active threat-group campaign), assign the correct specialist role (SOC L1, SOC L2, SOC Engineer, CERT Lead, Pen Tester, GRC Auditor, Threat Researcher) to each.

**My reasoning approach:**
- Initial alert triage → L1 (first filter on any raw alert)
- Deeper malware/technical analysis → L2 (more investigation depth, still within SOC)
- Active incident response (already compromised, in progress) → CERT Lead (highest-severity, cross-tool authority)
- Regulatory/compliance requirement (e.g., PCI DSS) → GRC Auditor (compliance-specific, not technical response)
- Proactive vulnerability discovery → Penetration Tester (offensive skill set)
- Infrastructure/tooling failure (e.g., SIEM down) → SOC Engineer (owns the tooling stack)
- Threat-actor tactics/motive analysis → Threat Researcher (intelligence function, not response)

This kind of role-matching is directly relevant to how real SOC escalation paths work — knowing *who* handles *what* is as important as the technical skills themselves.

## Why this matters for my transition

Coming from 10+ years in financial services, I already understand escalation hierarchies, compliance obligations, and cross-team coordination — this module made the parallels to SOC structure very clear to me.
