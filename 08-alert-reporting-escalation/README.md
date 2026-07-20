# SOC Alert Reporting & Escalation

**Lab context:** TryHackMe SOC Level 1 path — module on documenting investigations and deciding when/how to escalate to L2.
**Type:** Coursework / guided lab exercise

## What this module covered

Closing an alert with just a True/False Positive label isn't always enough — this module covered how to write a proper investigation report, when an alert needs to go to L2, and how to communicate with other departments (IT, HR) during an investigation.

## Key concepts (in my own words)

- **A good alert report saves the next person's time.** Raw SIEM logs typically only stay searchable for a limited retention window (often 3–12 months), while alert records with their comments are usually kept indefinitely — so writing a clear report isn't just for L2 today, it's the durable record of what happened.
- **The "Five Ws" structure works well for this**: Who was involved, What action/event occurred, When it happened, Where (device/IP/system), and Why — the reasoning behind the final verdict. Of the five, "Why" is the one that actually carries the analytical weight; the other four are just facts.
- **Escalation isn't a sign of weakness — it's a defined trigger, not a feeling.** The module laid out concrete reasons to escalate: the alert indicates a major attack needing deeper investigation, remediation actions (isolation, password reset, malware removal) are needed, external communication (customer, legal, law enforcement) is required, or the analyst genuinely doesn't understand the alert well enough to close it confidently.
- **Escalation is usually lightweight in practice** — reassign the alert to the on-shift L2 and flag them directly, backed by the report you already wrote. Some teams require a more formal ticket, but the report itself is what makes that handoff efficient either way.

## Practical exercise

I worked through prioritizing alerts by severity and age (most severe and oldest first, per the recommended queue-management approach) and self-assigning based on that order. I then wrote a Five-Ws-style report for a phishing-related alert — identifying the affected user, the exact suspicious action, a precise timestamp, the system involved, and a clear justification for the verdict (in this case, authentication artifacts like failed sender-verification checks plus a suspicious attachment, both indicators consistent with a credential-theft or malware-delivery attempt) — and made the call on whether it warranted escalation to L2 based on the criteria above.

## Why this matters for my transition

Writing a defensible justification for a decision — not just stating the decision — is something I've done for years in financial services when documenting exception cases and escalating them to credit or compliance teams. The Five Ws format is a more structured version of exactly that discipline, applied to security incidents instead of financial ones.
