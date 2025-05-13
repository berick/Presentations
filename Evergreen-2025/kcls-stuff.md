<!-- class: invert -->

<!-- Building: marp -w kcls-stuff.md -->

# What's New at KCLS and Why I Love Open Source

2025 Evergreen Conference

Bill Erickson

Software Development Engineer, King County Library System

[https://github.com/berick/Presentations/tree/main/Evergreen-2025](
    https://github.com/berick/Presentations/tree/main/Evergreen-2025)

---
# Two Big (For Us) 2025 Feature Deployments

---
# I. Automated Lost+Returned Refunds

<!-- 
replace clunky paper based system
patron had to request a refund! 
had to know they could!
-->

* Project first proposed in 2016.
* Business Applications (Evergreen), 
* Circulation,
* Finance.

---
# It's Complicated

* It's Money...
* Staff changes... 
* Design changes...
* Like, 100 pandemics...
* Went live early 2025

---
# Implemented in 2 Phases

<!--
Track refundable payments new table, links back to EG payments.

Re-printable, templated refundable payment receipts

===

Assessing refundability (item condition)
Applying refunded money to other charges
cutting checks / credit card refunds
issuing credit card refunds (not automatic)
-->

* Tracking Refundable Payments
* Automated Refund Processing

---
# To the Demo-Mobile!

---
# II. Patron Requests (Purchasing + ILL)

<!--
Replaces 2 seperate paper based forms
Much quicker turnaround
-->

* Deployed March
* Pushing high priority patches as late as last week.
* Still lots of work to do!

---

# Features

* Account verification
* Automated routing for Purchasing and ILL
* Duplicate / Existing Request Matching
* Staff UI for managing requests
* Staff mediated requests

---

# New Tech Alert
<!--
Material design more intentional; focused
with Accessibility in mind
doesn't do as much as Bootstrap
like how it looks
-->

* Angular Material
* Using Rust-based HTTP Gateway

---
# Back To the Demo-Mobile!

---
# Thanks!


