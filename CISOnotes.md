# CISO Module — Complete Exam Notes

All-in-one revision document: your original notes (cleaned up, errors fixed, gaps filled) plus the missing LG3 technology topics. Organised by theme rather than by your original page order, so related concepts sit together.

---

# PART A — FOUNDATIONS (LG1)

## A1. The CIA Triad

The three foundational goals of information security:

- **Confidentiality** — preventing sensitive information from being disclosed to unauthorised individuals, resources, or processes. Achieved via **encryption** and **multi-factor authentication (MFA)**.
- **Integrity** — protecting information from intentional and accidental modification. Maintained via **hash functions** and **checksums**.
- **Availability** — authorised users can access systems and data whenever required. Achieved via **hardware maintenance, software updates, and reliable backups**.

## A2. The Three Pillars — People, Process, Technology (PPT)

- **People** — the most important pillar. People are both the end-users and the designers/maintainers of systems. **Human action is the leading cause of cybersecurity incidents**, so secure systems must be designed *with* people in mind, not against them.
- **Process** — the administrative controls and guidelines for how tasks are performed. Effective processes are clear, easy to follow, accessible, and consistent — includes policies, incident response plans, and best-practice guidelines.
- **Technology** — the underlying infrastructure, software, and hardware solutions that protect systems: device encryption, network perimeters, firewalls, and monitoring for malicious activity.

## A3. The McCumber Cube (filling the empty gap)

Created by **John McCumber (1991)** — a 3D model to ensure security analysis is *complete*, by combining three dimensions. Every cell in the cube = one specific security concern.

| Dimension | Components |
|---|---|
| **Foundational principles** (the "what") | Confidentiality, Integrity, Availability — the CIA Triad |
| **Information states** (the "where") | Storage (at rest), Transmission (in transit), Processing (in use) |
| **Security measures** (the "how") | Policy & Practices (Process), Education/Training/Awareness (People), Technology |

3 × 3 × 3 = **27 cells**.

**Examples:**
- Confidentiality + Transmission + Technology → encrypting data in transit (TLS)
- Integrity + Storage + Policy → a backup/retention policy protecting archived records
- Availability + Processing + Education → training staff not to run unverified scripts that could crash production systems

**Why it matters:** a control addressing only one cell doesn't mean the data is protected everywhere — the cube forces a complete check across all states and measure types.

---

# PART B — THREATS & THREAT ACTORS (LG1)

## B1. Evolution of Threats

- **Disruption Era (1990s–2000s)** — dominated by viruses and worms designed to cause disruption and chaos.
- **Espionage Era (2010s)** — marked by targeted attacks, theft of intellectual property, and the rise of state-sponsored activity.

## B2. Threat Actors & Motivations

**MICE Framework** — used by criminologists to categorise *why* attackers act:
- **M** = Money
- **I** = Ideology
- **C** = Coercion
- **E** = Ego

**Threat actor profiles** (classified by skill level and backing):
- **Nation-State Sponsored (APTs — Advanced Persistent Threats)** — highly skilled groups conducting long-term espionage.
- **Cybercriminals** — professionals who buy **Crime-as-a-Service (CaaS)** — scripts or botnets — to generate profit.
- **Hacktivists** — driven by ideological or political motives, using disruption or data leaks.
- **Script Kiddies** — low-skilled individuals using primitive/off-the-shelf tools "because they can."

**Hackers by Hat:**
- **White Hat** — authorised, reports findings to owners.
- **Black Hat** — illegal, for personal gain.
- **Grey Hat** — unauthorised access, but may report findings if it suits their agenda.

## B3. Major Attack Vectors and Tactics

### Social Engineering & Human Behaviour
Attackers exploit psychological triggers:
- Authority
- Urgency
- Fear
- Curiosity
- Reciprocity
- Social proof

Common techniques:
- **Phishing** — deceptive emails designed to look legitimate
- **Pretexting** — creating a fabricated scenario to obtain privileged data
- **Tailgating** — following an authorised person into a physically secure location
- **Quid Pro Quo** — requesting information in exchange for a (fake) service

### Modern Ransomware — five-phase lifecycle
1. **Exploitation and Infection** — initial access via phishing or vulnerabilities
2. **Delivery and Execution** — malicious file runs, sometimes remaining dormant
3. **Backup Spoliation** — attackers target and delete backups to prevent recovery
4. **File Encryption** — secure key exchange with a command-and-control server occurs
5. **User Notification** — the ransom demand appears

### Distributed Denial of Service (DDoS)
Attacks that flood a target with traffic to overload systems — targets **Availability**. Often carried out using a **botnet** — a network of infected systems controlled by an attacker's server.

### Insider Threats
Threats from people who already have legitimate access — employees, partners, contractors:
- **Negligent** — careless actions or unintentional policy violations
- **Malicious** — intentional sabotage or theft
- **Compromised** — an external attacker takes over a legitimate internal account

## B4. The Cyber Kill Chain (Lockheed Martin)

A framework to identify where an attack can be interrupted at the earliest possible stage.

| Stage | Attacker Action | CISO Intervention Strategy |
|---|---|---|
| **Reconnaissance** | Harvesting emails, LinkedIn profiles, scanning | Limit public exposure, monitor scans |
| **Weaponization** | Building the malware payload | Hard to detect — happens on attacker's side |
| **Delivery** | Sending phishing emails or USB drops | Email filtering, web proxies, training |
| **Exploitation** | Triggering the vulnerability | Patching, application hardening |
| **Installation** | Establishing persistence on the asset | Antivirus, EDR tools, application whitelisting |
| **Command & Control** | Communicating with the compromised system | Network monitoring, firewall rules |
| **Actions on Objectives** | Stealing data or encrypting files | Data Loss Prevention (DLP), incident response |

---

# PART C — SECURITY FRAMEWORKS (LG1 & LG2)

## C1. Why Use a Framework?

Without a framework, organisations face chaos, inconsistent security decisions, and an inability to communicate effectively with the board.

A good framework answers the question: **"Are we secure?"** — and gives the CISO a defensible, structured way to demonstrate it.

## C2. ISO 27001 / 27002

- **Primary use:** Governance and **certification**.
- Focuses on building an **Information Security Management System (ISMS)** — a systematic approach to managing sensitive information.
- **Key feature:** the only major framework that allows **formal third-party certification**, often required in B2B environments and EU markets.
- Follows a **Plan-Do-Check-Act (PDCA)** continuous improvement cycle.
- ISO 27002 provides the detailed control guidance; Annex A of 27001 lists the controls themselves (organised into organisational, people, physical, and technological controls in the current version).

## C3. NIST Cybersecurity Framework (CSF) 2.0

- **Primary use:** Structure and **risk management**.
- CSF 2.0 organises activity into **six Core Functions**: **Govern, Identify, Protect, Detect, Respond, Recover**.
  - "**Govern**" was added in version 2.0 specifically — it's a cross-cutting function about establishing and overseeing the organisation's cybersecurity risk management strategy. (Note the parallel with NIS2's management-accountability requirement — both reflect the same shift toward board-level ownership of cyber risk.)
- **Implementation Tiers** — describe *how* an organisation manages cybersecurity risk (not a maturity score for the whole org, but how rigorously the Core is implemented):
  1. **Tier 1 — Partial**: ad hoc and reactive
  2. **Tier 2 — Risk Informed**: management is aware, but the approach isn't applied organisation-wide
  3. **Tier 3 — Repeatable**: a formal, consistent approach is followed
  4. **Tier 4 — Adaptive**: predictive, advanced, continuous-improvement culture

## C4. CIS Controls (Center for Internet Security)

- **Primary use:** Implementation and technical operations.
- A prioritised list of **18 critical security controls**.
- **Implementation Groups (IGs)**:
  - **IG1** — essential cyber hygiene; the baseline every organisation should implement first.
  - **IG2 & IG3** — progressively more advanced safeguards for organisations with higher risk profiles/resources.

## C5. MITRE ATT&CK

- **Primary use:** Threat intelligence and **threat-informed defence**.
- A matrix of real-world adversarial **tactics and techniques (TTPs)**.
- Flips the question: instead of "what controls should we have?" it asks **"what do attackers actually do?"** — so defences map directly to observed attacker behaviour.
- Connects to DORA's **Threat-Led Penetration Testing (TLPT)** — TLPT exercises are typically designed using ATT&CK TTPs.

## C6. Selecting the Right Strategy

CISOs combine frameworks because they overlap by roughly **70–80%** and complement each other:

- **Small companies:** Start with **CIS Controls IG1** for basic hygiene.
- **Medium companies:** **ISO 27001** for governance/certification + **CIS Controls IG2** for implementation.
- **Large multinationals:** **ISO 27001 (EU)** + **NIST CSF (US)** + **CIS IG3** + **MITRE ATT&CK**.

**Key facts:**
- A framework is a **tool, not a solution**.
- Framework selection is a strategic decision based on **industry, geography, and resources**.
- Actual security comes from **implementation and ongoing commitment** — not the framework on paper.

---

# PART D — GOVERNANCE, RISK & COMPLIANCE (GRC) (LG1 & LG2)

## D1. Governance vs Management

| | Governance | Management |
|---|---|---|
| Role | Sets direction, accountability, oversight | Handles execution, operations, enforcement |
| Question answered | **What and why** of security | **How and when** of security |
| Owned by | Board / senior executives — ensures security aligns with business goals | Operational teams |

**Failure mode:** if this separation breaks down, management can set its **own risk appetite** without oversight or policy — and is never audited. (NIS2's management-liability requirement exists specifically to prevent this.)

## D2. COBIT (Control Objectives for Information and Related Technologies)

The primary framework for **IT Governance**, built on five key principles:

1. **Meeting Stakeholder Needs** — balancing risk and resources to serve business objectives.
2. **Covering the Enterprise End-to-End** — moving beyond IT to include *all* information and technology across the business.
3. **Applying a Single Integrated Framework** — consolidating multiple governance standards into one system.
4. **Enabling a Holistic Approach** — considering culture, people, and processes alongside technology.
5. **Separating Governance from Management** — keeping direction distinct from execution (same concept as D1).

## D3. The Documentation Hierarchy

A structured hierarchy of documents used to organise security measures:

1. **Level 1 — Policies (Strategic)** — high-level statements of intent. *Example: "All employees must use MFA."*
2. **Level 2 — Standards (Tactical)** — specific requirements supporting policies. *Example: "MFA must use a hardware token, not SMS."*
3. **Level 3 — Procedures (Operational)** — step-by-step instructions. *Example: "How to enrol in the MFA portal."*
4. **Level 4 — Guidelines (Optional)** — recommended best practices. *Example: "Consider using a password manager."*

---

# PART E — RISK MANAGEMENT (LG2 & LG3)

## E1. Risk Appetite vs Tolerance

- **Risk Appetite** — the broad amount of risk an organisation is willing to accept strategically.
- **Risk Tolerance** — the specific, acceptable variation around that appetite.

## E2. Inherent vs Residual Risk

- **Inherent Risk** — the level of risk that exists if **no controls** were in place ("what if we did nothing?").
- **Residual Risk** — the risk that **remains after** security controls are applied.

## E3. Risk Assessment and Treatment

Risks can be assessed **qualitatively** or **quantitatively**. Once identified and assessed, there are **four treatment options**:

1. **Mitigate** — reduce the risk using technical or administrative controls.
2. **Transfer** — pass the risk to a third party (e.g. cybersecurity insurance).
3. **Accept** — acknowledge the risk and plan for the consequences.
4. **Avoid (Reject/Ignore)** — change plans to eliminate the risk entirely.

All risks and their treatments should be logged in a **Risk Register**.

## E4. Business Impact Assessment / Analysis (BIA) — NEW

Directly supports LG3's "assess business impact." A BIA identifies and evaluates the effects of a disruption to business functions/processes over time — it's the **bridge between risk assessment and business continuity planning**.

**Key outputs:**
- **RTO (Recovery Time Objective)** — the maximum acceptable downtime before a disruption causes unacceptable harm.
- **RPO (Recovery Point Objective)** — the maximum acceptable data loss, measured in time (e.g. "max 1 hour of data loss" → backups must run at least hourly).
- **MTPD (Maximum Tolerable Period of Disruption)** — the absolute outer limit before damage becomes irreversible (business failure, regulatory breach).

**How it connects:** the BIA tells you *which* assets/processes are critical → this informs **risk appetite/tolerance** (less tolerance for critical systems) → which informs the **risk treatment** decision (a process with a very short RTO likely needs Mitigation — e.g. redundant infrastructure — rather than Acceptance).

## E5. Threat Modeling — STRIDE

Developed by Microsoft. A structured way to ask **"what could go wrong?"** STRIDE categorises threats as:

- **S**poofing identity
- **T**ampering with data
- **R**epudiation
- **I**nformation disclosure
- **D**enial of service
- **E**levation of privilege

## E6. Third-Party & Supply Chain Risk

- Over **60% of breaches involve third parties**.
- The CISO is responsible for vendor failures and manages this risk through:
  - **Due diligence security audits**
  - **Software Bills of Materials (SBOM)** — a required list of all components and dependencies in a piece of software.

---

# PART F — EU REGULATORY LANDSCAPE (LG1)

Modern CISOs navigate a complex, overlapping regulatory landscape. Three critical frameworks:

- **GDPR** — protects personal data and privacy rights of individuals. Applies broadly, across all sectors.
- **NIS2 Directive** — expands cybersecurity requirements for a broad range of essential/important sectors, focusing on management accountability and supply chain security.
- **DORA** — targets the financial sector specifically, focusing on ICT-related risk management and third-party resilience.

## F1. GDPR — Key Articles

- **Article 32 — Security of Processing**
  - "The security article."
  - Requires **appropriate technical and organisational measures**, based on the current state of the art, implementation costs, and the level of risk to individuals.
  - Key features include **pseudonymisation, encryption**, and a process of **regularly testing and evaluating security**.

- **Article 28 — Processor Security**
  - Governs how organisations work with **vendors/processors**.
  - Organisations must ensure data processors provide sufficient security guarantees, formalised through **Data Processing Agreements (DPAs)**.

- **Article 33 — Breach Notification**
  - Mandates notifying regulators of a personal data breach **within 72 hours**.

- **Article 35 — Data Protection Impact Assessment (DPIA)**
  - A formal procedure required when data processing is likely to result in a **high risk** to individuals. Involves:
    - Describing the nature and purpose of the processing
    - Assessing whether the processing is necessary and proportional
    - Identifying risks to the rights and freedoms of individuals
    - Mitigating those risks through proposed measures

## F2. Data Handling Techniques

- **Anonymisation** — processing data so it can no longer be used to identify a natural person. Difficult to achieve fully for big data due to **linkability issues**.
- **Pseudonymisation** — replacing identifiable attributes with aliases. Unlike anonymisation, this is **reversible with a key** — but it's still a highly recommended measure because it **reduces the impact of a breach**.

## F3. NIS2 Directive — Expanded

**Scope — who is covered**, split into two categories:
- **Essential entities** — energy, transport, banking, financial market infrastructure, health, drinking water, wastewater, digital infrastructure (cloud, data centres, DNS), public administration, space.
- **Important entities** — postal/courier, waste management, chemicals, food, manufacturing (medical devices, electronics, vehicles), digital providers (marketplaces, search engines, social networks).

Generally **medium and large** organisations in these sectors are in scope.

**Management accountability (the headline new feature vs. the original NIS Directive):**
- Management bodies must **approve** and **oversee** the implementation of cybersecurity risk-management measures.
- Management can be held **personally liable** for non-compliance.
- Management must receive cybersecurity training.
- This directly closes the Governance-vs-Management failure mode from D1.

**Supply chain security:** organisations must explicitly assess and manage cybersecurity risk in supplier relationships — links to E6/SBOM.

**Incident reporting timelines (three stages):**
1. **24 hours** — early warning to the relevant CSIRT/authority
2. **72 hours** — detailed incident notification (assessment, severity, IoCs)
3. **1 month** — final report with full analysis and mitigation

**Penalties:** up to **€10 million or 2% of global annual turnover** for essential entities (whichever is higher).

## F4. DORA — Expanded

**Digital Operational Resilience Act** — applicable from **January 2025**. Targets the **financial sector**: banks, insurers, investment firms, payment/crypto-asset providers, and their **critical ICT third-party providers**.

**Five pillars:**
1. **ICT Risk Management** — governance framework; management body takes direct responsibility for ICT risk.
2. **Incident Reporting** — classification and reporting of major ICT-related incidents to regulators within set timeframes.
3. **Digital Operational Resilience Testing** — regular testing, including **Threat-Led Penetration Testing (TLPT)** for significant entities (uses MITRE ATT&CK-style TTPs).
4. **ICT Third-Party Risk Management** — strict oversight of critical providers, including a register of contractual arrangements. The EU can designate certain providers (e.g. major cloud platforms) as **"critical"** and supervise them directly via a **Lead Overseer**.
5. **Information Sharing** — voluntary cyber threat intelligence sharing between financial entities.

**Quick distinction:**
- **GDPR** → personal data, all sectors
- **NIS2** → broad essential/important sectors, resilience + incident reporting + management accountability
- **DORA** → financial sector only, ICT operational resilience + third-party/cloud oversight

## F5. Unified GRC Approach — "RESULT ONE"

To avoid duplicated effort across overlapping regulations, organisations build:

- **ONE Incident Response Process** — handles the varying notification timelines (e.g. 24h NIS2 early warning vs. 72h GDPR breach notification).
- **ONE Vendor Assessment Process** — covers requirements for all applicable regulations simultaneously.
- **ONE Governance Structure** — board reporting lines that satisfy all legal accountability requirements at once.

**Key takeaway:** Compliance is not about checking boxes — it's a **risk-based approach** where you must justify *why* your chosen measures are appropriate for *your* organisation's specific risks.

---

# PART G — MODERN TECHNOLOGY RISK EVALUATION (LG3) — NEW TOPICS

These three topics (Zero Trust, AI governance, cloud security) are named explicitly in LG3 and were missing from the original notes entirely.

## G1. Zero Trust

**Core principle: "Never trust, always verify."** No user, device, or system is trusted by default — regardless of whether it's inside or outside the traditional network perimeter.

**Why it replaced the old model:** the traditional "castle and moat" approach trusted anything inside the perimeter. Once an attacker (or a **compromised/malicious insider**, see B3) gets past the perimeter, they can move freely. Zero Trust **assumes the perimeter will be breached**.

**Reference model:** NIST SP 800-207 — Zero Trust Architecture (ZTA).

**Core tenets:**
- **Verify explicitly** — every access request is authenticated and authorised using all available signals: identity, device health, location, time, and resource sensitivity (this is where MFA fits in).
- **Least privilege access** — minimum necessary access, often via **just-in-time (JIT)** and **just-enough-access (JEA)** — temporary, scoped permissions rather than standing access.
- **Assume breach / microsegmentation** — the network is divided into small segments so a compromise in one area can't grant lateral access to everything. Directly limits the **Command & Control** and **Actions on Objectives** stages of the Cyber Kill Chain.

**Key components:**
- **Policy Decision Point (PDP)** — evaluates a request against policy, decides allow/deny.
- **Policy Enforcement Point (PEP)** — the gatekeeper that enforces the PDP's decision.
- Applies across five areas: **Identity, Devices, Networks, Applications/Workloads, Data**.

**Why a CISO cares:** Zero Trust is the practical answer to compromised insiders, remote/hybrid work (no "trusted office network"), and supply-chain compromise (a vendor's compromised account shouldn't grant broad access).

## G2. AI Governance

AI is both a **new attack surface** and a **new compliance domain**.

**New risks AI introduces:**
- **Data poisoning** — corrupting training data to manipulate model behaviour
- **Prompt injection** — manipulating an AI system's instructions via crafted input
- **Model theft / IP leakage** — extracting proprietary models or sensitive training data
- **Shadow AI** — employees using unauthorised AI tools with company data (a new flavour of negligent insider risk)

**NIST AI Risk Management Framework (AI RMF)** — voluntary, four core functions:
- **Govern** — build a culture and structure for AI risk management
- **Map** — understand the context an AI system operates in; identify risks specific to it
- **Measure** — assess and track risks using appropriate metrics
- **Manage** — prioritise and respond to risks, allocate resources

**The EU AI Act** — a **risk-based regulation** (same philosophy as GDPR), classifying AI systems into tiers:
- **Unacceptable risk** — banned outright (e.g. social scoring, manipulative techniques, most real-time biometric categorisation). Prohibitions + AI-literacy obligations applied **from Feb 2025**.
- **High-risk** — used in recruitment, credit scoring, critical infrastructure, medical devices, etc. Subject to risk management systems, technical documentation, human oversight, conformity assessments (echoes ISO 27001 third-party certification).
- **Limited risk** — transparency obligations only (e.g. chatbots must disclose they're AI). Applies **from Aug 2026**.
- **Minimal risk** — no specific obligations.

**General-Purpose AI (GPAI)** models (e.g. LLMs) have separate obligations — documentation, transparency, copyright compliance — applying **from Aug 2025**.

*(Exact dates have been subject to ongoing adjustment via a 2026 "Digital Omnibus" simplification — for the exam, focus on the **risk-tier structure** and the **Govern/Map/Measure/Manage** functions rather than memorising exact dates.)*

**CISO's role:**
- Establish an **AI Acceptable Use Policy** (fits Documentation Hierarchy Level 1)
- Maintain an **AI system inventory** (an SBOM-equivalent for AI components)
- Apply **vendor due diligence** to third-party AI tools (links to E6)
- Define **human oversight** requirements for high-risk AI use cases

## G3. Cloud Security

### Shared Responsibility Model
Security responsibility is **split** between provider and customer, and the split depends on the service model:

| Service Model | Provider responsible for | Customer responsible for |
|---|---|---|
| **IaaS** (raw VMs) | Physical infrastructure, hypervisor, network | OS, patching, applications, data, IAM, configuration |
| **PaaS** (managed platform) | Infrastructure + runtime/OS | Applications, data, access management |
| **SaaS** (ready-to-use software) | Almost everything | Data, user access management, configuration choices |

**Critical point:** even in SaaS, the customer is **always** responsible for their **data** and **who has access to it**. "The cloud provider handles security" is a dangerous misconception.

### Common cloud risks
- **Misconfiguration** — the single largest cause of cloud data breaches (e.g. an accidentally public storage bucket)
- **Excessive permissions / weak IAM** — violates least privilege (links to Zero Trust)
- **Insecure APIs** — cloud services are API-driven; a poorly secured API is a direct path to data
- **Lack of visibility** — shadow IT, multi-cloud sprawl, unmanaged accounts
- **Data residency / sovereignty** — where data is physically stored matters for GDPR cross-border transfer rules

### Relevant tools/concepts
- **CSPM (Cloud Security Posture Management)** — continuously scans for misconfigurations
- **CASB (Cloud Access Security Broker)** — sits between users and cloud services for visibility/policy enforcement
- **CSA Cloud Controls Matrix (CCM)** — cloud-specific controls framework from the Cloud Security Alliance (a cloud equivalent of CIS Controls)

**Connection:** cloud security is fundamentally a **third-party/supply chain risk** problem — your cloud provider *is* a vendor, so due diligence, DPAs (GDPR Art. 28), and SBOM-style visibility all apply.

---

# QUICK-REFERENCE CHEAT SHEET

## Key numbers & timelines
| Item | Number/Timeline |
|---|---|
| GDPR breach notification | 72 hours |
| NIS2 early warning | 24 hours |
| NIS2 incident notification | 72 hours |
| NIS2 final report | 1 month |
| NIS2 fines (essential entities) | Up to €10M or 2% global turnover |
| Third parties involved in breaches | Over 60% |
| Framework overlap | 70–80% |
| CIS Controls total | 18 controls, 3 Implementation Groups |
| McCumber Cube | 3 × 3 × 3 = 27 cells |
| STRIDE | 6 categories |
| CIA Triad | 3 principles |
| PPT pillars | 3 pillars |
| NIST CSF 2.0 Core Functions | 6 (Govern, Identify, Protect, Detect, Respond, Recover) |
| NIST CSF Implementation Tiers | 4 (Partial → Risk Informed → Repeatable → Adaptive) |
| Documentation Hierarchy | 4 levels (Policy → Standard → Procedure → Guideline) |
| Risk treatment options | 4 (Mitigate, Transfer, Accept, Avoid) |
| COBIT principles | 5 |
| AI Act risk tiers | 4 (Unacceptable, High, Limited, Minimal) |
| DORA pillars | 5 |
| AI RMF functions | 4 (Govern, Map, Measure, Manage) |

## Acronym glossary
- **APT** — Advanced Persistent Threat
- **BIA** — Business Impact Analysis
- **CaaS** — Crime as a Service
- **CASB** — Cloud Access Security Broker
- **CCM** — Cloud Controls Matrix
- **CSF** — Cybersecurity Framework (NIST)
- **CSPM** — Cloud Security Posture Management
- **DLP** — Data Loss Prevention
- **DORA** — Digital Operational Resilience Act
- **DPA** — Data Processing Agreement
- **DPIA** — Data Protection Impact Assessment
- **EDR** — Endpoint Detection and Response
- **GPAI** — General-Purpose AI
- **IG (1/2/3)** — Implementation Group (CIS Controls)
- **IoC** — Indicator of Compromise
- **ISMS** — Information Security Management System
- **JIT/JEA** — Just-in-Time / Just-Enough-Access
- **MICE** — Money, Ideology, Coercion, Ego
- **MTPD** — Maximum Tolerable Period of Disruption
- **PDCA** — Plan-Do-Check-Act
- **PDP/PEP** — Policy Decision Point / Policy Enforcement Point
- **RPO/RTO** — Recovery Point/Time Objective
- **SBOM** — Software Bill of Materials
- **STRIDE** — Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Elevation of privilege
- **TLPT** — Threat-Led Penetration Testing
- **TTP** — Tactics, Techniques, and Procedures
- **ZTA** — Zero Trust Architecture

---

# Mapping to Learning Goals (for exam focus)

- **LG1 (60% — Understand)**: Parts A, B, C, D, F, plus McCumber Cube and the AI Act/NIS2/DORA expansions in G2/F3/F4.
- **LG2 (20% — Apply)**: Part C6 (selecting frameworks), Part E3 (risk treatment), Part D3 (documentation hierarchy) — practice *applying* these to a scenario, not just reciting them.
- **LG3 (20% — Analyse)**: Part E1–E5 (risk analysis, BIA, STRIDE) + Part G (Zero Trust, AI governance, cloud security) — practice *evaluating* a technology choice using risk-based criteria (likelihood/impact, shared responsibility, verification vs. implicit trust).