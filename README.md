<div align="center">

<br>

# GraphRX

## The Old Guard is Running Out of Time.

<br>

**Healthcare claims is a $300 billion graph problem being solved with spreadsheets.**<br>
**We built the graph.**

<br>

---

**2.08 µs** full claim adjudication. **3.57 million** transactions per second. Single core.

**4.8 million** messages parsed per second. **63** fraud patterns in real time. **< 50 µs** total.

**43** invariants enforced at pre-commit. **Zero** illegal states. Ever.

---

</div>

<br>

## This is a Manifesto.

The US healthcare transaction industry processes **9 billion claims per year** on
architectures designed when Reagan was president. COBOL mainframes. Relational
databases. Batch fraud detection that discovers theft **days after the money is
gone**. Enrollment systems that can't answer "was this member covered on that
date?" without a thirty-table join that takes minutes.

The cost of this architectural debt is not abstract. It is
**$300 billion per year** in fraud, waste, and abuse. It is pharmacies dispensing
opioids to dead patients. It is providers billing for procedures that never
happened. It is collusion rings operating for years because no system can see
the pattern — because the pattern is a *graph*, and the data lives in *rows*.

The industry knows this. The industry has known this for decades. And the
industry's response has been to bolt more middleware onto the same broken
foundation — a new API layer here, a machine learning model there, a cloud
migration that moves the same relational tables to slightly faster hardware.

**That era ends now.**

<br>

## To the Incumbents

You built your claims engines on relational databases in the 1990s. You've spent
thirty years patching them. You have thousands of engineers maintaining millions
of lines of COBOL, Java, and stored procedures that no single human being fully
understands.

Your fraud detection runs overnight. By the time you find the fraud, the money
is three states away. Your audit teams spend weeks reconstructing state that
should be a single query. Your "AI strategy" is a black-box model bolted onto
a system that can't explain its own decisions — and regulators are coming for
that.

We're not building a better version of what you have. **We replaced the
foundation.**

<br>

## To the "Cloud-Native Disruptors"

You took the old architecture and put it on AWS. Congratulations. You replaced
on-premise relational databases with cloud-hosted relational databases. You
replaced COBOL with Python. You replaced batch jobs with Lambda functions that
run the same batch logic on a 15-minute schedule.

You raised hundreds of millions of dollars. Some of you are valued in the
billions. And you still can't:

- Answer a bitemporal query ("what did we believe about this member's coverage
  on March 1st, *as our records understood it on March 15th*?")
- Detect a collusion ring in real time (because your data model can't *see*
  the ring — it's a graph pattern stored across fifty tables)
- Guarantee that an illegal state can never be written (because your validation
  lives in application code that can be bypassed, forgotten, or deployed with a
  bug)
- Explain *why* a claim was denied in terms a regulator or a judge can follow
  (because your rules engine is a black box, not a traversable graph of
  evidence)
- Reproduce a test failure deterministically (because your systems depend on
  wall-clock time, network ordering, and thread scheduling that change between
  runs)

You modernized the *infrastructure*. You didn't modernize the *architecture*.

And the architecture is the problem.

### Your Testing Is a Joke

Let's talk about how you verify a system that processes hundreds of billions of
dollars in healthcare transactions.

Playwright. End-to-end browser tests that click through a UI. Unit tests that
mock the database, mock the network, mock the message queue — mock everything
that matters — and then assert that the mocks behaved like mocks. A QA team
running manual regression scripts in a staging environment that hasn't matched
production since the day it was provisioned.

That's it. That's the testing strategy for a system adjudicating pharmacy claims
for tens of millions of Americans.

No fault injection. No deterministic simulation. No property-based invariant
verification. No compressed-time chaos testing. No formal proof that illegal
states can't persist. Just Playwright clicking buttons and Jest checking that
`mockDatabase.save()` was called with the right arguments. Your mocks pass. Your
production breaks. And nobody can reproduce the failure because your tests don't
control time, network ordering, or I/O scheduling — so the bug only happens on
Tuesdays when the message queue is slow and the cache expires at the wrong
moment.

You know this. Your engineering team knows this. Your Jira board is full of
tickets tagged "intermittent" and "cannot reproduce" and "works in staging."
Those aren't mysteries. They're symptoms of a testing methodology that was
designed for CRUD apps, not financial infrastructure.

### The MVP That Ate Your Runway

Here's what actually happened. You built an MVP. A quick serverless prototype
to prove the concept. Lambda functions, DynamoDB tables, an API gateway,
maybe some Step Functions for orchestration. It worked. Investors loved the
demo. You raised your Series A.

Then someone made the decision that every startup makes and every startup
regrets: **ship the MVP to production.**

That prototype — the one with no invariant enforcement, no temporal model, no
formal testing, no fraud prevention layer — is now processing real claims for
real patients. And instead of replacing it with a proper system, you've spent
every subsequent funding round bolting features onto the prototype. A new
microservice here. Another Lambda there. A data pipeline to work around the fact
that your DynamoDB tables can't do the join you need. A caching layer to hide
the latency of the six network hops required to adjudicate a single claim.

**Your architecture diagram isn't a diagram. It's an archaeological dig.** Every
layer is a monument to a deadline that was more important than doing it right.
Every microservice is a workaround for a limitation in the microservice below
it. Your 130 APIs aren't a feature — they're a confession. A well-designed
system doesn't need 130 APIs. It needs a coherent data model.

And the runway? You've raised over half a billion dollars. Your burn rate is
tens of millions per month. Hundreds of engineers maintaining a system that
started as a weekend prototype. Your cost per claim is going *up*, not down,
because every new feature adds another service, another database, another
integration, another team, another Slack channel, another incident rotation.

You're not building a platform. You're feeding a bloat rocket. And every dollar
you raise is another gallon of fuel burning on the way to a reckoning with
the question you've been avoiding since Series A:

**Is this the architecture, or is this the prototype?**

It's the prototype. It was always the prototype. And the market is about to find
out.

<br>

## What We Built

GraphRX is not a claims processing system. It is not a fraud detection platform.
It is not a compliance tool.

**GraphRX is a transaction kernel.** A temporal graph engine purpose-built for
healthcare, where every relationship is a typed, temporal edge, every mutation
is an event, every state is reconstructable, and illegal states are
*structurally impossible*.

Healthcare data is not rows. It is members enrolled in plans. Providers
contracted with networks. Pharmacies dispensing for payers. Claims linking
members to providers to pharmacies to drugs. Payments settling claims. Every one
of these is a **relationship with a time dimension**. A graph with temporal
edges.

We stored it as one.

<br>

## The Numbers

Every number below is measured. Not estimated. Not projected. Not "up to."
Statistical benchmarks, 100 samples each, CI regression gating. If a pull
request makes any of these numbers worse by more than 10%, it does not merge.

| What We Do | How Fast | What They Do | How Fast |
|------------|----------|-------------|----------|
| Adjudicate a pharmacy claim, end to end | **2.08 µs** | Same operation, traditional stack | 200–800 ms |
| Process transactions (single core) | **3.57 million/sec** | Same operation, traditional stack | ~800/sec |
| Parse an NCPDP message | **4.8 million/sec** | Same operation, middleware stack | ~50,000/sec |
| Score a claim for fraud (63 patterns) | **< 50 µs** | Same operation (if they do it at all) | Overnight batch |
| Check drug interactions (20 medications) | **977 nanoseconds** | Same operation, stored procedure | 10–50 ms |
| Detect a collusion ring | **40 µs** | Same operation, network analysis | Weekly batch job |
| Answer a bitemporal query (10K records) | **28 µs** | Same operation, multi-table join | Minutes |
| Reconstruct historical state for audit | **257 µs** | Same operation, backup tape archaeology | Days to weeks |

The difference is not optimization. It is **elimination**. When your storage
model matches your domain model, you stop spending 95% of your compute
translating between the two.

We didn't make joins faster. We eliminated joins. There is nothing left to
optimize because there is nothing left that is unnecessary.

<br>

## What Makes This Architecturally Impossible to Retrofit

These are not features. They are consequences of the foundational design. You
cannot add them to an existing relational claims platform any more than you can
make a submarine fly by bolting on wings.

### Bitemporal Truth

Every fact in the system carries two time dimensions: when it was true in the
real world, and when the system recorded it. This is not a pair of
`effective_date`/`term_date` columns. It is the storage model itself.

The question "what did we believe about this member's formulary tier on January
15th, as of our records on February 1st?" is a single indexed lookup. Not a
query. Not a reconstruction. A lookup. **28 microseconds.**

Retrofitting bitemporality onto a relational schema means rewriting every table,
every query, every report, every integration. It is a ground-up rebuild. No one
with a live production system and 54 million members is doing that.

### Illegal States Are Impossible

Forty-three constraints are evaluated synchronously before every write.
NPI passes Luhn-10. Opioid MEDD doesn't exceed threshold without prior
authorization. No overlapping enrollment periods on the same plan type. Payment
amount equals the sum of allowed line items.

These aren't application-level validations that run in a middleware layer. They
are kernel-level invariants enforced between the mutation and storage. There is
no code path — no bug, no race condition, no deployment error — that can write
an inconsistent state.

Traditional systems validate in application code. Application code has bugs.
Bugs ship. Bugs create inconsistent state. Inconsistent state creates fraud
surface. That cycle is structurally impossible here.

### Graph-Native Fraud Detection

Sixty-three fraud patterns. Not rules running against flat tables. Graph
traversals running against the actual relationship structure of the data.

A pill mill isn't a row in a table with a suspicious number. It's a pharmacy
node with an anomalous ratio of controlled substance edges to prescriber edges,
relative to its geographic peer cohort. A collusion ring isn't a statistical
outlier. It's a biclique in the provider-pharmacy bipartite graph. An impossible
travel pattern isn't a timestamp comparison. It's a pair of claim edges from the
same member node to provider nodes whose geographic distance exceeds what is
physically possible in the elapsed time.

These patterns are trivial to express and execute when the data is a graph. They
are expensive, fragile, and often impossible when the data is tables.

All 63 patterns evaluate per-claim, in real time, in under 50 microseconds
total. Not overnight. Not weekly. **Every claim, every time, before the response
goes back to the pharmacy.**

### Three Layers of Fraud Defense

Most systems have one fraud defense: detection, running in batch, finding
yesterday's fraud tomorrow. We have three:

**Layer 1 — Prevention.** The invariant engine blocks entire categories of fraud
structurally. You cannot submit a claim for a terminated provider. You cannot
enroll a member in two plans of the same type simultaneously. You cannot
write a payment that doesn't balance against its claim lines. These aren't
detected after the fact. They cannot happen. Cost: zero. It's a pre-commit
check.

**Layer 2 — Detection.** Sixty-three graph patterns evaluate in real time during
adjudication. Pill mills, phantom pharmacies, doctor shopping, collusion rings,
impossible travel, unbundling, upcoding — all caught before the claim is paid.
Cost: under 50 microseconds per claim.

**Layer 3 — Retrospective.** The complete event history enables temporal
analytics across any time window. Provider X's prescribing pattern shifted 40%
toward Schedule II after a contract change six months ago? That's a query, not
an investigation. Cost: milliseconds.

The industry standard is Layer 3 only, running in batch. We start at Layer 1.

### Every Decision Is Explainable

When this system denies a claim or flags fraud, it doesn't say "the model said
so." It produces a **graph traversal path**: the specific nodes, edges, temporal
states, and invariant evaluations that led to the decision.

This isn't just good engineering. It's a legal requirement that's coming for
everyone. The EU AI Act (August 2026 for high-risk systems), CMS guidelines on
AI-driven utilization review, Colorado SB 24-205, California SB 1120 — all
demand explainability that black-box ML cannot provide.

We didn't build explainability as a feature. Explainability is the architecture.
Every decision is a path through the graph. The path *is* the explanation.

### AI That Cannot Cause Harm

AI reads the graph. AI cannot write to the graph. Period.

AI generates proposals — "this claim appears fraudulent," "this prior
authorization should be approved." Those proposals become commands. Those
commands pass through the same forty-three invariant checks that every other
mutation passes through. If the AI hallucinates, the invariant engine catches
it.

In a world where every competitor is racing to give AI write access to claims
systems, we chose the only architecture that is safe: **AI advises, the kernel
decides.** There is a formal, enforced boundary between AI inference and state
mutation. No prompt injection, no hallucination, no adversarial input can cross
that boundary.

### Deterministic Simulation Testing

A single 64-bit seed controls the
entire simulation: time, network, storage, scheduling, fault injection. Any
failure is reproduced by re-running with the same seed. Not "probably
reproduced." Identically reproduced. Bit for bit.

Over six hundred test variants. Thirty-two fault types injected at every I/O
boundary. A reference oracle verifying consistency and liveness properties. One
simulated year of production chaos compressed into minutes.

No healthcare platform in the world tests like this. Most can't even reproduce a
test failure reliably, let alone compress a year of edge cases into a seed.

<br>

## The Market Is Moving. Fast.

The regulatory environment has shifted from "detect fraud after payment" to
"prevent fraud before payment." The catalysts are stacking:

**CMS CRUSH Initiative** (February 2026) saved $2 billion in its first year.
The government is done with pay-and-chase. They want pre-payment prevention —
which is exactly what a graph-native invariant engine provides.

**CMS-0057-F** mandates FHIR APIs for prior authorization, patient access, and
payer-to-payer data exchange by January 2027. Every payer needs to comply.

**DOJ healthcare False Claims Act recoveries** hit an all-time record of
$5.7 billion in FY 2025, with a record 1,297 whistleblower filings. The
enforcement pressure is unprecedented.

**The Change Healthcare attack** (February 2024) — $6.3 billion in impact, 94%
of hospitals affected — proved that centralized clearinghouse architecture is a
single point of failure the industry can no longer afford.

**74% of payer CIOs** report their current systems cannot keep pace with
AI-enabled fraud schemes.

**Administrative cost per claim** rose from $43.84 in 2022 to **$57.23** in
2023. The economics of the old way are deteriorating, not improving.

The payers who act first will own the next decade. The ones who wait will spend
it explaining to regulators why their 1990s architecture couldn't stop fraud
that a graph database would have caught in 40 microseconds.

<br>

## What's Coming

This is not a roadmap. This is a warning.

**Open-source kernel.** The temporal graph transaction engine — the core that
makes everything else possible — will be open-sourced under Apache 2.0. The
first open-source healthcare claims infrastructure in history. The industry has
open-source EHRs and open-source FHIR servers. It has **zero** open-source
claims engines, **zero** open-source pharmacy adjudication, **zero** open-source
healthcare fraud detection. We're filling every one of those gaps.

**Native protocol fluency.** NCPDP D.0. X12 837/835/270-271/278. FHIR R4.
Parsed and generated natively — not through middleware, not through translation
layers, not through third-party libraries that add 50ms of latency. The system
speaks the industry's languages as a native speaker, not through an interpreter.

**AI staff augmentation.** Purpose-built for healthcare operations. Every
clinical, financial, and administrative role — claims analysts, SIU
investigators, pharmacists, compliance officers — augmented by AI that
understands the domain because it reads the graph directly. Not a chatbot
bolted onto a search engine. A domain-aware intelligence layer with formal
safety boundaries.

**Managed cloud, certified.** SOC 2 Type II. HITRUST. URAC AI Accreditation
(the first healthcare-specific AI certification, launched September 2025). Not
because certifications are hard to get, but because they are the price of
admission — and we will have them before the first enterprise deployment.

<br>

## To the Industry

Here is what we know about you:

Your core adjudication engine was written before some of your engineers were
born. Your "modernization" project is a React frontend on a REST API that calls
the same COBOL. Your fraud team finds fraud that happened last week and writes a
report that someone reads next month. Your compliance team spends six weeks
preparing for an audit that GraphRX answers in 257 microseconds. Your AI
strategy is a vendor contract with no explainability guarantee, and the EU AI
Act deadline is eighteen months away.

You have two choices:

**1. Build what we built.** Hire a team. Spend three years. Write 229,000 lines
of code with 40,000 lines of specifications. Implement a bitemporal graph
kernel, an invariant engine, 63 fraud patterns, protocol drivers for every
healthcare standard, deterministic simulation testing, and AI safety
architecture. Do it in a memory-safe language with zero unsafe code. Ship it
with 5,289 tests and 190 benchmarks. Then maintain it.

**2. Use ours.**

The second option is faster.

<br>

## To the Builders

We are looking for people who believe healthcare infrastructure should be built
with the same rigor as databases, operating systems, and financial settlement
networks. People who think "it works on my machine" is not an acceptable
standard for a system that processes $722 billion in pharmaceutical transactions
annually.

If you've ever been frustrated by healthcare technology that is slow, fragile,
inexplicable, and decades behind every other industry — this is the project that
fixes it. Not by adding another layer. By replacing the foundation.

<br>

## The Bet

GraphRX is a bet that the healthcare transaction industry will, within the next
five years, undergo the same architectural revolution that happened to
databases (PostgreSQL replacing proprietary), operating systems (Linux replacing
proprietary), and cloud infrastructure (commodity replacing mainframe).

The incumbents will say it's impossible. That healthcare is too regulated, too
complex, too entrenched. They said the same thing about every industry that got
rebuilt from first principles.

The $300 billion in annual fraud losses says the current approach isn't working.
The Change Healthcare attack says the current architecture isn't resilient. The
CMS CRUSH initiative says the government is done waiting. The record DOJ
enforcement says the consequences are real.

The question is not whether the architecture changes. The question is who builds
the replacement.

**We already did.**

<br>

---

<div align="center">

<br>

**Graph-native. Bitemporal. Invariant-enforced. Deterministically tested. AI-safe.**

**Built in Rust. Measured in microseconds. Proven in benchmarks.**

**The future of healthcare transactions isn't coming. It's here.**

<br>

**[graphrx.dev](https://graphrx.ai/)** (coming soon)

<br>

</div>
