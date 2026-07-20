# SOC Workbooks & Context Lookups

**Lab context:** TryHackMe SOC Level 1 path — module on gathering supporting context (identity, asset, and network information) during alert triage.
**Type:** Coursework / guided lab exercise

## What this module covered

Alert triage rarely stands alone — a raw alert (e.g., "user X logged into server Y") is meaningless without context about who that user is, what that server does, and how the network is laid out around it. This module covered the reference sources an L1 analyst pulls from to answer those questions quickly.

## Key concepts (in my own words)

- **Identity inventory** is essentially a directory of every user and service account in the company — role, working hours, privilege level, and typical access patterns. Without it, an analyst has no way to judge whether "G.Baker logging into a finance server at 2am" is routine or alarming — you can't detect an anomaly if you don't know what normal looks like for that specific identity.
- **Asset inventory** does the same job for infrastructure — what a given server is for, who's authorized to touch it, and where it physically or logically sits in the network. This turns a bare hostname into something actionable.
- **Network diagrams** let you reconstruct an attack path visually rather than guessing from raw IPs and ports. Given a sequence of firewall log entries, a diagram shows you which subnet an IP belongs to and whether a connection attempt crosses a boundary it shouldn't.

## Practical exercise: reconstructing an attack path

Given a sequence of firewall log events — an external IP repeatedly hitting a corporate firewall on an unusual port, then appearing internally via NAT translation, then scanning two internal subnets in succession — I worked through matching each IP/port/subnet to the network diagram to reconstruct the likely attack chain: external brute-force attempt against a VPN endpoint, a successful login granting an internal VPN-subnet IP, then lateral scanning attempts against more sensitive subnets (blocked in one case, redirected to a softer target in the next).

**My takeaway on the reasoning process:** the individual log lines look almost boring in isolation — a connection here, a scan there. The network diagram is what turns them into a coherent story of an attacker moving from external access toward higher-value internal targets. This is the core skill: not memorizing IPs, but being able to place any given IP/port/identity into its proper context fast enough to matter during triage.

## Why this matters for my transition

This module reinforced something I already do instinctively from banking: I never assess a transaction or account activity in isolation — context (client profile, account history, typical behavior) is what turns a raw data point into a decision. The SOC version of that instinct is identity/asset/network lookups, and building the habit of reaching for those references before making a call is directly transferable.
