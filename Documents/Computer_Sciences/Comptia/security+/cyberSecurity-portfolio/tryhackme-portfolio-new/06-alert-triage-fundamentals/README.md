# SOC L1 Alert Triage — Foundations

**Lab context:** TryHackMe SOC Level 1 path — introductory alert-handling module using a simulated SIEM dashboard.
**Type:** Coursework / guided lab exercise

## What this module covered

The full lifecycle of a SOC alert: how an alert is generated from raw events, what fields it carries (time, name, severity, status, verdict, assignee), and how an L1 analyst is expected to pick up, investigate, and resolve one from a shared queue.

## Key concepts (in my own words)

- **Self-assignment discipline matters.** Before picking an alert, you check that no other analyst already has it — grabbing an alert someone else is working means duplicated effort and confusion during handoff.
- **Prioritization is severity-first, then age.** Given a queue of open alerts, the correct approach is Critical → High → Medium → Low, and within the same severity tier, oldest first. This mirrors basic triage logic in any high-volume queue, not just security.
- **Status changes communicate to the team**, not just to yourself. Moving an alert to "In Progress" the moment you start working it signals to colleagues that it's covered, so they move to the next item in line instead of duplicating work.
- A verdict (True Positive / False Positive) is not the end of the process — the alert still needs to be formally **closed**, and most platforms require a documented justification (an analyst comment) before that closure is accepted. Skipping this step is a common early mistake.

## Practical exercise: triaging a live queue

I worked through a simulated SIEM queue containing 5 alerts of mixed severity and status (open items ranging from Low to Critical, plus two already-closed historical alerts for context). My process:

1. **Prioritized correctly** — identified "Potential Data Exfiltration" (Critical) as the first pick, followed by "Double-Extension File Creation" (High), then "Download from GitHub Repository" (Low)
2. **Investigated each independently** — for the double-extension case, I reasoned through why a file like `invoice.pdf.exe` is a classic technique for disguising a malicious executable as a document (a masquerading technique — MITRE ATT&CK T1036.007)
3. **Set a verdict with justification** — wrote an analyst comment explaining the reasoning for each verdict, not just the True/False Positive label itself
4. **Escalated appropriately** — for the Critical and High severity confirmed threats, reassigned to an L2 analyst with an escalation note in the comment, since deeper investigation and containment exceed L1 scope; for the Low-severity GitHub download, verified the source/publisher and closed it myself as a False Positive since it matched a known, approved internal tool

## Why this matters for my transition

This is the most literal, hands-on version of the job itself — a real SOC L1 analyst spends most of their day exactly like this: picking up alerts by priority, gathering just enough context to make a defensible call, documenting the reasoning, and knowing precisely when a case is theirs to close versus when it needs to go up the chain.
