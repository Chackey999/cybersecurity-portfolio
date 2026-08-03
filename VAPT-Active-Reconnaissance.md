# VAPT — Active Reconnaissance

**Targets:** Self-hosted lab VM (bWAPP/XAMPP on Windows 10) and, for route
mapping only, the previously identified public IP for `ummedcyber.com`
**Environment:** Kali Linux + Windows 10, both VirtualBox VMs (Host-Only
network)
**Phase:** Active Reconnaissance — direct interaction with target
infrastructure
**Author:** Mamadou "Amad" Koulemou

## Overview

This writeup covers the active reconnaissance phase of my VAPT lab
training, picking up where [passive reconnaissance](./VAPT-Passive-Reconnaissance.md)
left off. Unlike the previous phase, every technique here sends traffic
directly to the target — which is why the bulk of this exercise was
deliberately shifted onto a **self-hosted lab VM** (bWAPP running on a
Windows 10 VirtualBox VM) rather than continuing against the live
production domain used for passive recon. Scanning infrastructure you
don't own or have explicit authorization to test crosses from lab
practice into something that requires real permission — so once port
scans against the production target came back uniformly filtered
(expected behavior for a firewalled production host), I pivoted to a
target I fully control.

What follows is as much a network-troubleshooting log as a tooling
writeup — most of the real learning in this phase came from diagnosing
*why* scans weren't working, not from running the scans themselves.

---

## 1. Host Discovery

**Objective:** Confirm the target is live and reachable before running
any deeper scans.

**What I did:** Used `nmap` for host discovery instead of a bare ICMP
ping sweep, since ICMP is commonly blocked by network security controls
and isn't a reliable discovery method on its own.

### Troubleshooting chain (the actual learning here)

My first scan came back with **all 1000 scanned ports filtered** — no
response at all, the signature of something silently dropping every
probe. I worked through this systematically rather than assuming the
target was simply unreachable:

1. **Windows Defender Firewall.** Checked the Windows 10 VM's firewall
   settings and found both the Private and Public profiles actively
   blocking unsolicited inbound connections. Disabled the firewall on
   both profiles via Control Panel to rule it out as the cause.
2. **Result unchanged — still fully filtered.** This told me the
   firewall wasn't the (only) problem, so I moved to checking basic
   connectivity with `ping` directly.
3. **Ping also failed.** At this point the issue was clearly below the
   application layer — a routing/addressing problem, not a
   firewall/service problem.
4. **Root cause: network adapter mismatch.** Both VMs were on **Bridged
   Adapter** mode, which should have placed them on the same physical
   LAN — but connectivity was still being dropped. Switching both VMs to
   **Host-Only Adapter** put them on a proper shared virtual network
   segment, and connectivity started working immediately.
5. **IP addresses changed as a side effect of the network switch** — Kali
   picked up a new address via DHCP automatically, while the Windows VM
   required manually configuring a static IP (Control Panel → Network
   and Sharing Center → Adapter Settings → IPv4 Properties) since it
   didn't automatically request one on the new segment.

Once addressing was sorted, `ping -c 4 <target-ip>` succeeded, and the
follow-up `nmap -v <target-ip>` host discovery scan returned a clean
"host is up" with responsive ports — confirming the entire earlier
"filtered" result had been a network configuration issue, not a target
security control.

**Skill developed:** Layered network troubleshooting — learning to
isolate *which layer* is actually causing a failure (application/firewall
vs. transport/routing vs. link-layer addressing) instead of guessing at
fixes. Also gained hands-on comfort with VirtualBox networking modes
(Bridged vs. Host-Only) and manual static IP configuration on Windows.

**Lesson learned:** A "filtered" nmap result can mean several
completely different things — a target firewall, a host-based firewall,
*or* a fundamental network misconfiguration that never even gets packets
to the target in the first place. Don't assume the first plausible cause
is the actual cause; verify each layer before moving to the next
troubleshooting step.

---

## 2. Full Port and Service Scan

**Command:** `nmap -sV -sC -p- <target-ip>`

**Objective:** Enumerate all 65,535 TCP ports, identify running service
versions, and run Nmap's default NSE (Nmap Scripting Engine) scripts
against discovered services — now that the underlying transport issue
from step 1 was resolved.

**Skill developed:** Understanding the tradeoffs of a full-range scan
(comprehensive but slow) versus targeted scans, and reading combined
`-sV -sC` output to distinguish raw port state from service
fingerprinting and script-based enumeration results.

---

## 3. Targeted Scan (Common Web/DB Ports)

**Command:** `nmap -sV -sC -p 80,443,3306,3389 <target-ip>`

**Objective:** Faster, iterative scanning focused on the ports most
likely to matter for a web application target (HTTP/S, MySQL, RDP)
rather than waiting on a full 65k-port sweep for every iteration.

**Skill developed:** Scoping a scan to the situation — recognizing when
a full scan is appropriate (initial baseline) versus when a targeted
scan is the more efficient choice (fast follow-up checks during active
testing).

---

## 4. Service Version Detection

**Command:** `nmap -v -p 80,3306 -sV <target-ip>`

**Objective:** Isolate version detection specifically on the two most
relevant ports (web and database) for closer inspection.

**Skill developed:** Narrowing scope further once initial results
identify which services actually warrant deeper attention — moving from
broad enumeration toward focused investigation, mirroring how a real
assessment progressively narrows its focus.

---

## 5. Route Mapping

**Commands:** `traceroute <target-ip>`, `ping <target-ip>`
**Target for this step:** the public IP identified during passive recon
(`118.139.180.x` / `ummedcyber.com`)

**Objective:** Map the network path to the target and confirm
reachability at the IP layer.

### A methodology note worth stating explicitly

I initially considered filing traceroute/ping under the *passive*
reconnaissance phase, since a reference text I was studying grouped them
there. On reflection I disagreed with that categorization: both tools
send packets directly to the target's own infrastructure (ICMP echo
requests, or UDP/ICMP probes with incrementing TTL) rather than querying
a third-party data source. That makes them **active** techniques by
definition, regardless of how low-impact or non-disruptive they are —
so I've filed them here instead, under Active Reconnaissance, where they
belong.

**Skill developed:** Applying the passive/active distinction correctly
and consistently, rather than deferring to a source's categorization
without checking it against the actual definition (does the technique
query a third party, or does it touch the target directly?).

**Why route mapping matters (beyond just "does it work"):** identifying
the network path to a target can reveal intermediate infrastructure
(load balancers, additional routing hops, potential rogue devices on a
network), help verify a network's compliance with expected topology, and
surface latency bottlenecks along the path — all useful context for
later phases of an assessment.

---

## 6. OS Fingerprinting

**Command:** `nmap -O <target-ip>`

**Objective:** Attempt to identify the target's operating system based
on TCP/IP stack behavior characteristics.

**Skill developed:** Understanding that OS fingerprinting is
probabilistic, not definitive — Nmap infers the OS from subtle
differences in how different operating systems implement the TCP/IP
stack, and results should be treated as a strong hint to corroborate with
other findings (banners, default ports, service behavior), not a
guaranteed fact.

---

## 7. Directory Enumeration

**Command:** `dirb http://<target-ip>/mutillidae`

**Objective:** Discover hidden or unlinked directories and files within
a target web application by brute-forcing common paths against a
wordlist.

**Skill developed:** First hands-on use of a directory brute-forcing
tool against a real (self-hosted, intentionally vulnerable) web
application — understanding how this technique surfaces application
structure that isn't visible through normal browsing, which directly
feeds into later vulnerability assessment work.

---

## Key Takeaways

- **Most of the real skill-building in this phase happened before a
  single meaningful scan result appeared.** Diagnosing the filtered/
  firewall/network-mismatch chain took more systematic troubleshooting
  than any individual nmap flag did — and that troubleshooting is a more
  realistic representation of real-world engagement work than a scan
  that "just works" on the first try.
- **Authorization boundaries shaped my target selection.** Once the
  production domain returned a uniformly filtered result (consistent
  with a properly firewalled host), I deliberately moved active,
  packet-sending techniques onto infrastructure I fully own, rather than
  pushing more aggressive scanning against a target without explicit
  authorization for active testing.
- **The passive/active boundary is a definition to apply, not a label to
  copy.** Route mapping tools get filed inconsistently across sources —
  I chose to classify them based on the actual mechanism (direct target
  interaction = active) rather than deferring to how one reference
  happened to organize its chapters.

## Skills Summary (for quick reference)

| Skill | Demonstrated via |
|---|---|
| Layered network troubleshooting | Diagnosing filtered results across firewall → transport → link layer |
| VirtualBox networking modes | Bridged vs. Host-Only adapter configuration |
| Manual static IP configuration | Windows 10 network adapter setup |
| Nmap host discovery | `-v`, `-Pn`, ARP/ICMP behavior on local networks |
| Full & targeted port scanning | `-p-` vs. scoped `-p` scans |
| Service/version detection | `-sV`, `-sC` (NSE default scripts) |
| Route mapping | `traceroute`, `ping`, correctly classified as active recon |
| OS fingerprinting | `nmap -O` and its probabilistic nature |
| Directory enumeration | `dirb` against a self-hosted vulnerable web app |

---

*Previous phase: [Passive Reconnaissance](./VAPT-Passive-Reconnaissance.md)*
*Next phase: Vulnerability Assessment*
