# Quantia Pay: Incident Response

> Bootcamp lab: simulated scenario (TripleTen Cybersecurity, 2026)

## Context
It's 3:14 AM and a P1 alert fires at Quantia Pay, a payment
processor that never stops: the CI/CD pipeline's service account
logged in from an unusual host, enumerated Active Directory, and
opened a connection to an unknown external IP, with data already
leaving the network. The catch: payments can't go down, so
containment couldn't mean "shut everything off". This was an
incident simulator, so every decision had a consequence.

## What I did
- Declared the incident using three conditions (real threat,
  affected assets, need for a coordinated response) and marked the
  breakpoint at the first anomalous login, separating it from
  legitimate pipeline activity.
- Contained it without touching production: network isolation
  through EDR (keeping the host's memory alive) plus a /32 firewall
  rule on port 443 aimed only at the attacker's IP. Exfiltration
  stopped at 6.9 GB, against an estimated 47 GB without containment
  (about 85% less).
- Captured volatile evidence in order of volatility: network
  connections, ARP cache, RAM, disk image, logs.
- Found the root cause: the service account token was exposed in a
  public build log because the pipeline wrote secrets unmasked.
- Eradicated the attacker's persistence (a scheduled task and a
  registry Run key) without touching legitimate services. My first
  attempt left one behind and the attacker came back; I corrected
  it and verified 6 of 6 checks, each with evidence.
- Rotated the token, patched the pipeline, and created a
  monitoring rule.
- Recommended rebuilding the compromised jump host (2-hour recovery
  target, no impact on payments).
- Wrote a five-part executive summary (BLUF) that separates what is
  confirmed from what isn't, such as whether the 6.9 GB included
  card data.
- Mapped 9 control gaps (preventive, detective, corrective) and
  wrote 3 prioritized recommendations, each with an owner, a date,
  and a test to verify it.

## Tools & methods
IR Console simulator, EDR network containment, firewall rules,
evidence preservation, persistence hunting, BLUF reporting,
post-incident control-gap analysis.

## Results
Simulator score 98/100 (Session Log included in the report), zero
legitimate services touched, payments unaffected, and the content
of the exfiltrated data still to be confirmed.

## Deliverables
- Report with Session Log: /Proyecto 6 Respuesta a incidentes.pdf

## Instructor review
Approved without reservations. Praised for the self-corrected
re-infection and an honest executive summary.

## What I learned
My first eradication attempt failed: I removed one persistence
mechanism, missed the other, and the attacker came back. I'd
rather tell you that than hide it. I noticed right away, went back,
removed both, and verified. That's the lesson: eradication isn't
finished until every check has a fact behind it, not a feeling
that it's clean. I also learned how much a few minutes matter: the
alert fired about twelve minutes after the first login, and that
window is where 6.9 GB left, so detection is where I'd invest
first.
