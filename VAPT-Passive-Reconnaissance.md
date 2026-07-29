# VAPT — Passive Reconnaissance

**Target:** `ummedcyber.com` (training/lab domain)
**Environment:** Kali Linux (VirtualBox VM)
**Phase:** Passive Reconnaissance — no direct interaction with target infrastructure
**Author:** Mamadou "Amad" Koulemou

## Overview

This writeup documents the passive reconnaissance phase of a Vulnerability
Assessment and Penetration Testing (VAPT) exercise performed as part of my
self-directed lab training. The goal of this phase was to gather as much
information as possible about the target **without sending any traffic
directly to its infrastructure** — relying instead on public records, DNS
registries, third-party aggregators, and search-engine-indexed data.

Every finding below reflects data collected purely through third-party
sources (WHOIS registries, DNS records, certificate transparency logs,
Shodan's pre-existing scan cache, and OSINT aggregators). At no point did I
send a probe, request, or packet directly to the target's own servers — that
distinction is the whole point of this phase, and it's a distinction I had
to actively defend more than once (see the "Methodology Correction" note at
the end).

---

## 1. WHOIS Lookup

**Objective:** Establish domain ownership, registrar, registration dates,
and name server information as a starting baseline.

**What I did:** Queried the target domain through a WHOIS lookup to pull
registrant/registrar metadata and confirm the authoritative name servers.

**Skill developed:** Reading and interpreting WHOIS output — separating
what's actually actionable (name servers, registrar, creation date) from
noise, and understanding that most registrants now sit behind privacy
proxies, which shapes what OSINT can realistically yield later.

**Lesson learned:** WHOIS data is often the *first* signal that a domain
sits on shared infrastructure (e.g., a registrar-provided nameserver rather
than a dedicated one) — a signal I later had to actively correlate against
IP-level findings to avoid a mistake described below.

---

## 2. DNS Enumeration (`dnsenum`)

**Objective:** Map the target's DNS footprint — A records, name servers,
and any exposed zone data.

**What I did:** Ran `dnsenum` against the target domain to enumerate host
addresses and authoritative name servers.

**Findings:**
- Resolved the target's primary A record to a specific host IP
- Identified two GoDaddy-managed name servers (`ns15`/`ns16.domaincontrol.com`)
- Zone transfer attempt did not succeed (expected — a properly configured
  DNS server should refuse unauthenticated zone transfers)

**Skill developed:** Comfort running and interpreting core DNS
enumeration tooling, and recognizing that a failed zone transfer is
itself a meaningful (positive) security finding, not a dead end.

**Alternative tools noted for future practice:** `nslookup`, `dnsrecon`,
`dnsmap`, `theHarvester`, `Sublist3r` — logged as tools to revisit for
comparison/cross-validation of results.

---

## 3. DNSDumpster

**Objective:** Cross-validate DNS findings using a second, independent
source and discover subdomains not surfaced by `dnsenum` alone.

**What I did:** Ran the same target domain through dnsdumpster.com and
compared its A/MX/NS/TXT record output against my `dnsenum` results.

**Findings:** Confirmed the same host IP and name server infrastructure
found via `dnsenum`. No MX or TXT records were publicly exposed for this
domain.

**Skill developed:** Cross-tool validation as a habit — treating a single
tool's output as a hypothesis to confirm, not a fact to report on its own.

---

## 4. IP & Reverse Lookup (ViewDNS.info / Shodan)

**Objective:** Use the resolved IP address to (a) understand the hosting
environment and geographic location, and (b) identify any other domains
sharing the same server.

**What I did:** Took the IP resolved via DNS enumeration and ran it
through ViewDNS.info's Reverse IP Lookup, then cross-referenced the same
address in Shodan.

### Methodology correction (an important lesson, not a footnote)

My first attempt at this step produced misleading results: I mistakenly
used a **network/subnet identifier** (an IP ending in `.0`, pulled from
the ASN block shown alongside the real host IP) instead of the actual
resolved host address. Reverse-looking-up a `.0` address returned an
entirely unrelated domain in Shodan — a red flag that told me something
was wrong before I wrote a single line of the wrong finding into a report.

Once I corrected this and re-ran the lookup against the **actual host
IP**, the results made sense — confirming the target sat on GoDaddy shared
hosting, alongside several unrelated tenant domains.

**Why this matters for the writeup:** shared hosting is common,
and reverse IP lookups on shared infrastructure will surface
**"neighbor" domains that have nothing to do with the actual target.**
Learning to tell "genuinely related infrastructure" apart from
"coincidentally co-hosted noise" is a core recon skill — and I got that
distinction wrong once before getting it right, which is exactly the kind
of mistake this training environment exists to catch safely.

**Findings (on the corrected IP):**
- Confirmed hosting provider: GoDaddy.com, LLC (shared hosting, Singapore
  data center)
- Open ports consistent with a shared mail/web hosting stack (SSH, SMTP,
  HTTP/S, POP3/IMAP, cPanel admin ports)
- Web technology fingerprint: WordPress 6.9.4, PHP 8.4.22, Apache HTTP
  Server, MySQL
- A list of CVEs associated with the *detected software versions* —
  correctly understood as candidate hypotheses to verify later, not
  confirmed vulnerabilities (Shodan explicitly caveats this, and I made a
  point of not treating it as a finding on its own)

**Skill developed:**
- Interpreting ASN/subnet data correctly and not confusing a network
  identifier with a host address
- Reading Shodan host pages (open ports, banners, hostnames, vulnerability
  hints) and understanding what each panel actually represents
- Applying basic Shodan search filter syntax (`country:`, `port:`,
  `net:`, `org:`, `hostname:`) — including learning the difference
  between `org:` (organization name, e.g. `"GoDaddy.com, LLC"`) and
  `hostname:` (domain-based lookup), after an initial query mixing a raw
  IP with a mismatched `org:` filter returned zero results

**Lesson learned:** A wrong IP produces *convincing-looking* wrong data —
Shodan and ViewDNS will happily return a fully-formed, professional-looking
result for whatever you actually typed in, whether or not it's the right
target. Verifying the input before trusting the output is not optional.

---

## 5. OSINT Aggregation (`theHarvester`)

**Objective:** Search third-party indexed sources (search engines,
certificate transparency logs, etc.) for emails, subdomains, and hosts
associated with the target — entirely passively, since every data point
comes from a source that already scanned or indexed the target
independently of my activity.

**What I did:** Ran `theHarvester` against the target domain using
multiple source backends (`-b all`, then isolated runs against `crtsh`
and others) after an initial run against the wrong subdomain format
(`www.` prefix) returned nothing.

**Findings:**
- 3 associated IP addresses, including a secondary IP not previously
  surfaced by DNS enumeration alone — flagged for follow-up
- 7 subdomains, including an exposed `admin.` subdomain — noted as an
  observation worth flagging in a real engagement (not exploited, simply
  documented)
- No email addresses were discoverable via passive OSINT sources at the
  time of testing — a legitimate negative result, not a tooling failure

**Skill developed:** Understanding that `theHarvester`'s source list and
supported engine names change across versions (e.g., `google`/`bing` were
unsupported in the installed 4.10.1 release), and building the habit of
checking `--help` / engine support rather than assuming flags from
documentation or tutorials will match the installed version exactly.

**Lesson learned:** An empty result from one source (e.g., `crtsh`
returning nothing) is not the same as "no data exists" — it means that
*specific* dataset didn't have anything indexed. Running multiple sources
before concluding "no findings" is what separates a thorough passive recon
pass from an incomplete one.

---

## Key Takeaways

- **Passive reconnaissance is not "no-skill" work.** Every tool in this
  phase required correctly interpreting third-party data, catching
  self-introduced errors (like the `.0` IP mistake), and knowing when a
  negative result is meaningful versus when it just means "try a
  different source."
- **Cross-validation is the discipline that makes passive recon
  trustworthy.** No single tool's output was treated as final — DNS
  findings were checked against a second DNS tool, and IP findings were
  checked against both ViewDNS and Shodan before being written down.
- **Methodology boundaries matter.** I initially considered including
  `traceroute`/`mtr` results under this passive recon section, since a
  reference text I was studying grouped them there. On reflection, both
  tools send packets directly to the target's own infrastructure with
  incrementing TTLs to map the network path — which makes them **active**
  reconnaissance by definition, regardless of how low-impact they are.
  That distinction (does the technique query a third party, or does it
  touch the target directly?) is the actual line between passive and
  active recon, and I'm applying it consistently rather than following a
  source that filed it differently.

## Skills Summary (for quick reference)

| Skill | Demonstrated via |
|---|---|
| WHOIS interpretation | Domain ownership/registrar analysis |
| DNS enumeration | `dnsenum`, cross-validated with DNSDumpster |
| ASN / subnet vs. host IP distinction | Corrected reverse-lookup methodology |
| Shodan search syntax & filters | `country:`, `port:`, `net:`, `org:`, `hostname:` |
| OSINT aggregation | `theHarvester` across multiple source backends |
| Cross-tool validation discipline | Every finding checked against ≥2 independent sources |
| Passive vs. active methodology boundaries | Correctly excluding traceroute/mtr from this phase |

---

*Next phase: Active Reconnaissance (network path mapping via
traceroute/mtr, port scanning, and direct service enumeration).*
