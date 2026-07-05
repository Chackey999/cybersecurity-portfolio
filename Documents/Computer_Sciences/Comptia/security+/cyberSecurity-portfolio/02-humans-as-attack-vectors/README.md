# Humans as Attack Vectors — Social Engineering & Security Policy

**Lab context:** TryHackMe SOC Level 1 path — social engineering module with a practical policy-selection exercise.
**Type:** Coursework / simulated SOC dashboard exercise

## What this module covered

Social engineering — phishing, malware downloads via fake CAPTCHAs/SEO poisoning, deepfake impersonation, and physical/insider vectors like USB drops. The core idea: attackers often find it easier to manipulate a person than to breach a firewall.

## Key takeaways (in my own words)

- Social engineering succeeds when it looks **trustworthy** and triggers an **emotional response** (urgency, fear, curiosity) — that combination is what bypasses rational scrutiny.
- Defense splits into two categories: **mitigation** (reduce likelihood/impact before an attack lands — training, anti-phishing tooling) and **detection** (catch what gets through — SOC monitoring, alerting).
- Neither category alone is sufficient. Mitigation will never be 100% effective, which is exactly why the SOC detection layer exists.

## Practical exercise: security policy selection

Given a list of candidate security policies, the task was to select the four most impactful for reducing human-vector risk (as opposed to general technical hardening).

**My selections and reasoning:**

| Policy | Why it addresses the human vector |
|---|---|
| Security Awareness Program | Trains employees to recognize and report phishing — attacks the root cause rather than a symptom |
| Anti-Phishing Solution | Removes malicious emails before an employee has a chance to be manipulated |
| Access Management Policy | Prevents attackers from social-engineering IT support into resetting credentials or granting access |
| Internet Restrictions | Reduces employee exposure to malicious sites/infrastructure they could be tricked into visiting |

**What I ruled out and why:** options like antivirus or vulnerability scanning are valuable, but they're technical/reactive controls — they don't address the *decision point* where a human is tricked into acting. For a module specifically about the human vector, I prioritized controls that intervene before or at that decision point.

## Why this matters for my transition

This kind of reasoning — distinguishing root-cause controls from surface-level ones — is the same evaluative thinking I use in financial services when assessing fraud or compliance risk. It's a transferable skill I lean on heavily as I move into security analysis.
