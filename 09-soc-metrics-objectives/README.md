# SOC Metrics & Objectives

**Lab context:** TryHackMe SOC Level 1 path — module on how SOC team performance is measured (SLA, MTTD, MTTA, MTTR, and related ratios).
**Type:** Coursework / guided lab exercise

## What this module covered

How SOC efficiency gets quantified, why those numbers matter beyond box-checking, and — most relevantly for an L1 analyst — how individual triage habits directly move the metrics a team is judged on.

## Key concepts (in my own words)

| Metric | What it measures | Why it matters |
|---|---|---|
| Alerts Count | Overall daily load per analyst | Too high risks missed threats in the noise; too low can mean a detection/visibility gap, not a quiet day |
| False Positive Rate | Share of alerts that turn out to be noise | High FPR breeds alert fatigue — analysts start treating everything as "probably nothing," which is exactly when a real threat slips through |
| Alert Escalation Rate | How often L1 hands an alert up to L2 | A rough proxy for analyst experience/confidence — too high may mean under-training, too low may mean overconfidence on cases that needed a second set of eyes |
| Threat Detection Rate | Share of actual attacks the SOC caught | The metric with zero acceptable margin — a missed attack isn't a bad percentage, it's a real incident |
| MTTD / MTTA / MTTR | Time to detect / acknowledge / respond | Together these form the backbone of most SOC Service Level Agreements (SLAs), whether internal or with an MSSP client |

## Key takeaway

The metric that struck me most is that **Threat Detection Rate is the one metric that should always be 100%** — everything else (False Positive Rate, escalation rate, alert volume) can be tuned and optimized over time, but a missed real attack has no "acceptable range." That reframes the other metrics correctly: they're operational health indicators, but TDR is the actual mission.

I also noted the practical, individual-analyst levers for improving metrics rather than treating them as abstract KPIs: reducing false positives by tuning detection rules for known-trusted activity, keeping acknowledgment time low by working the queue promptly rather than letting it pile up, and escalating only when a real trigger is met (see [`08-alert-reporting-escalation`](../08-alert-reporting-escalation)) rather than reflexively — since escalation rate is itself a tracked metric.

## Why this matters for my transition

Coming from banking, I'm used to being measured against SLA-style targets (file turnaround times, error/exception rates, compliance deadlines). Recognizing that SOC metrics work the same way — and that my day-to-day triage habits are what actually move them — made this module feel less like new material and more like a direct mapping of a discipline I already have.
