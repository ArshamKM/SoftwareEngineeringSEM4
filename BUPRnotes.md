# BUPR – Complete Exam Notes
### Everything in one place: BPM + ERP + BPMN + Choreographies

---

# PART 1 — BPM FOUNDATIONS

## Business Process

A business process is a set of activities performed in coordination within an organizational and technical environment in order to jointly realize a business goal.

Think of it like a relay race — each runner (activity) passes the baton (information or materials) in a specific order to reach the finish line (the goal). What makes it a process rather than just work is that it involves **coordination** and a **business goal**.

## Business Process Management (BPM)

BPM is the discipline of concepts, methods and techniques used to support the design, administration, configuration, enactment and analysis of business processes. In other words, it is the science of managing and improving how a company works.

The analogy: the coach doesn't run the race, but designs the plays, analyzes performance and improves the strategy.

## Business Process Management System (BPMS)

A BPMS is a generic software system driven by explicit process models that coordinates the process enactment — basically the software that tells employees what to do next.

**BPM vs BPMS: BPM is the discipline. BPMS is the tool.**

A BPMS contains: process models, a process engine, a model repository, process instances, service integrations, human task handling, and monitoring/analysis functions.

---

## The Four Levels of Business Processes

1. **Business Goals/Strategy** — long-term objective, like becoming the top seller in a market
2. **Organizational Business Processes** — high-level text descriptions of inputs and outputs
3. **Operational Business Processes** — detailed sequences of activities, regardless of technical execution
4. **Implemented Business Processes** — actual execution within a technical environment (e.g. SAP)

## The Business Process Lifecycle

1. Design and Analysis
2. Configuration
3. Enactment
4. Evaluation

---

## Activity Instance Lifecycle (State Transitions)

- **Ready** — task is waiting for a person or system to start it
- **Running** — work is actively being performed
- **Terminated** — work is finished successfully
- **Skipped** — task was bypassed, common in decision-making paths

---

# PART 2 — CONTROL FLOW PATTERNS

## The Big Three

**Sequence** — Activity A must finish before Activity B starts. The simplest form.

**AND (Parallel Split/Join)** — tasks can happen at the same time.
- AND Split: one path becomes multiple parallel paths
- AND Join (synchronizer): does NOT move forward until ALL incoming paths have finished. Waits for everyone.

**XOR (Exclusive Choice)** — exactly one path is chosen based on a condition (e.g. if order is above €100).
- XOR Join moves forward as soon as ANY ONE incoming path finishes. No waiting.

**OR (Inclusive Choice)** — one or more paths can be chosen. More flexible than XOR but more complex because the join has to wait for all ACTIVE paths while ignoring the inactive ones.

**Summary: AND = wait for everyone. XOR = pick exactly one. OR = pick one or more.**

---

## Advanced Patterns

**Multiple Merge** — multiple paths lead into a merge point and the downstream activity is triggered multiple times — once for each path that completes. This can cause the same activity to run more than once.

**Discriminator** — like a race. The FIRST path to finish triggers the next activity immediately. The discriminator then waits for all remaining paths to finish (ignoring them) before resetting itself for the next run.

**N-out-of-M Join** — the process proceeds only after N paths out of a total of M have been completed. Example: wait for 2 out of 3 managers to approve.

**Arbitrary Cycles** — the process can loop back to an earlier point. Example: review → missing info → request correction → review again.

**Implicit Termination** — the process ends automatically when no active work remains.

**Deferred Choice** — the process does not choose the path immediately. The environment or the first occurring event decides. Example: after sending a quote, either the customer accepts, rejects, or the quote expires — whichever happens first determines the path.

**Multiple Instances** — several instances of the same activity are created. Three variants:
- With a priori design-time knowledge: number of instances known before execution (always request exactly 3 supplier quotes)
- With a priori runtime knowledge: number known during execution (send reminders to all currently overdue customers)
- Without a priori runtime knowledge: number not known and can change during execution. A management activity must explicitly signal when no more instances can be created.

---

# PART 3 — ARIS AND EPC

## What is ARIS?

ARIS (Architecture of Integrated Information Systems) is a holistic framework used to analyze, optimize and structure business processes BEFORE implementation in software like ERP.

## The ARIS House

Think of ARIS as a house:

- **Organization (roof)** — departments, roles, reporting lines, like an org chart
- **Data (pillar 1)** — what information is needed, business objects like customers and orders, represented as ER diagrams
- **Function (pillar 2)** — what tasks are performed, listed as a function tree without ordering
- **Control (central pillar / heart)** — uses EPCs to connect all the pillars and tell the story of how data, people and functions work together in a sequence

## Three Levels of Abstraction in ARIS

1. **Concept Level** — business-friendly requirements definition. Simple EPCs that managers can understand.
2. **Architecture/Design Level** — translates business models into IT terms like database structures.
3. **Implementation Level** — actual technical realization: writing code or configuring SAP tables.

---

## EPC — Event-driven Process Chain

An EPC is the modelling notation used inside ARIS to describe procedural business processes.

### EPC Building Blocks

**Functions** (rounded rectangles) — active units of work or tasks. Example: Check stock, Create invoice.

**Events** (hexagons) — represent a state change or condition. Example: Order received, Payment confirmed. Events trigger functions and are also produced by functions.

**Connectors** (circles) — define the logical flow:
- AND (∧): all paths must occur
- XOR (X): exactly one path occurs
- OR (V): one or more paths occur

**Control flow** — dashed arrows connecting elements in a causal order.

### Formal EPC Syntax Rules

**Alternation Rule** — the sequence must always be: Event → Function → Event → Function. You cannot have two functions in a row or two events in a row.

**Edges** — each function must have exactly one incoming and one outgoing edge (unless a connector splits/joins).

**Starts/Ends** — at minimum one start event (no incoming edge) and one end event (no outgoing edge).

**The Decision Rule** — events are PASSIVE. Events cannot be followed by an XOR or OR split because events cannot make decisions. Only functions can lead to a split.

---

## Extended EPCs

Basic EPCs just show events and functions. Extended EPCs add "satellites" around the functions:

- **Organizational Units** — attached to functions to show who is responsible
- **Application Systems** — show which software (e.g. SAP) performs the task
- **Information Carriers** — show which documents or data are used as input or output

These can be shown directly on the EPC or moved to a separate **Function Allocation Diagram (FAD)** to reduce visual clutter.

---

## RASCI

RASCI lines connect people/roles to functions in an EPC:

- **R (Responsible)** — carries out the task
- **A (Accountable)** — decides on the task
- **S (Supportive)** — contributes to the task
- **C (Consulted)** — provides advice
- **I (Informed)** — must be told about the result

---

# PART 4 — BPMN

## What is BPMN and Why Does it Exist?

BPMN (Business Process Model and Notation) is a standardized notation readable by everyone — business analysts who design processes, developers who implement them, and managers who monitor them. It creates a **standardized bridge** between business process design and technical implementation.

**EPC is great for internal ARIS analysis. BPMN is the industry-wide standard used in practice.**

---

## The Four Categories of BPMN Elements

**Flow Objects** — things that do something: Events, Activities, Gateways

**Connecting Objects** — lines between things:
- Sequence Flow (solid arrow) — order of activities WITHIN one pool
- Message Flow (dashed arrow with open head) — communication BETWEEN pools
- Association (dotted line) — links annotations to elements

**Artefacts** — extra information:
- Data Object — shows data used or produced
- Group (dashed rectangle) — visually groups elements, does NOT affect flow
- Annotation (bracket) — adds a note or comment

**Swimlanes** — who does what:
- Pool — represents one participant (a whole company or system)
- Lane — a role or department INSIDE a pool

**Critical rule: Sequence flow stays inside one pool. Message flow crosses between pools. Never mix them up.**

---

## Activity Types

**Task** — the basic unit of work. A single step.

**Subprocess** — an activity with its own internal process. Two forms:
- Collapsed: internal steps hidden, shown with + symbol
- Expanded: internal steps visible inside the box

**Call Activity** — references a globally defined reusable process stored externally (thick border). Like calling a function in code — reused across multiple diagrams.

**Transaction Activity** — a group of tasks that must succeed or fail as a whole (double border). If one step fails, all completed steps inside are compensated/rolled back. Example: book hotel + charge credit card — if card fails, cancel the hotel.

---

## Activity Markers

Markers sit at the bottom of the activity box and refine its behaviour:

| Marker | Symbol | Meaning |
|--------|--------|---------|
| Subprocess | + | Has internal structure |
| Loop | ↺ | Repeats until a condition is met or stops being met |
| Parallel MI | ⦀ three vertical lines | Multiple instances running in parallel |
| Sequential MI | ≡ three horizontal lines | Multiple instances running one after another |
| Adhoc | ~ tilde | Tasks inside can run in any order, multiple times, or be skipped |
| Compensation | ◄◄ | Used to undo already-completed steps |

**Loop example:** Suggest dishes until everyone agrees, then prepare the meal.
**Parallel MI example:** Every roommate chooses their pizza topping simultaneously.

---

## Task Types

| Task Type | Icon | Meaning |
|-----------|------|---------|
| Send Task | filled envelope | Sends a message to an external participant |
| Receive Task | empty envelope | Waits to receive a message from outside |
| User Task | person icon | Human performs it using software |
| Manual Task | hand icon | Human performs it WITHOUT any software |
| Business Rule Task | grid icon | A business rule engine processes it. Rules can be changed outside the model. |
| Service Task | gear icon | Uses a web service or automated application |
| Script Task | scroll icon | Process engine executes a script automatically |

---

## Ad-Hoc Subprocess

Marked with ~. Activities inside can be executed in any order, executed multiple times, or skipped entirely. Example: preparing a lecture — you might do slides before content, or revisit the outline multiple times.

---

## Events

Events represent something that happens. Three positions:

- **Start Event** — thin single border circle. Every process starts with one.
- **Intermediate Event** — thin double border circle. Occurs between activities.
- **End Event** — thick single border circle. Every process has at least one.

**Catching event** — waits for a trigger to fire it. Shown with an outline icon (hollow inside).
**Throwing event** — the process itself fires it. Shown with a filled icon.

### Event Types

| Type | Description |
|------|-------------|
| None/Blank | Simple start, state change, or end. No specific trigger. |
| Message | Receives or sends a message (envelope icon) |
| Timer | Time-based trigger — after X hours, at a specific date, cyclic |
| Error | Catches or throws named errors. When thrown inside a subprocess, all running activities are interrupted. |
| Cancel | Reacts to cancelled transactions or triggers cancellation |
| Compensation | Triggers compensation (undo) of completed activities |
| Signal | Broadcast — one signal can be caught by multiple processes |
| Link | Off-page connector. Two matching link events equal a sequence flow across pages. |
| Terminate | End event that immediately ends the ENTIRE process |

### Boundary Events

Attached to the border of an activity. Fires while the activity is running.

**Interrupting boundary event** (solid border) — fires → activity is cancelled → alternative path runs.

**Non-interrupting boundary event** (dashed border) — fires → alternative path runs → original activity CONTINUES.

**Timer boundary event example:** A subprocess runs. After 5 hours (non-interrupting T2): send a status update, subprocess continues. After 12 hours (interrupting T1): cancel the subprocess, write a preliminary report.

---

## Gateways

| Gateway | Symbol | Split | Join |
|---------|--------|-------|------|
| Exclusive (XOR) | X or plain diamond | Exactly one path, conditions evaluated in order, default (/) last | First arrival continues |
| Parallel (AND) | + | All paths activated | Wait for all |
| Inclusive (OR) | O circle | One or more paths | Wait for all activated paths |
| Event-based | ⬡ pentagon | Whichever event occurs first | N/A |
| Complex | * | Custom advanced logic (e.g. 3 of 5 branches) | Custom logic |

**Event-based gateway vs XOR:** XOR decides based on a DATA CONDITION evaluated immediately. Event-based gateway WAITS for an external event to occur.

**Default flow in XOR:** The default path (marked with /) is always evaluated LAST, after all other conditions fail.

**Livelock warning:** Activities with multiple outgoing edges implicitly act as parallel splits. This can cause modelling errors. Always use explicit gateways.

---

## Data in BPMN

- **Data Object** — data used or produced by an activity. Can show states: [received], [valid], [invalid], [accepted], [processed], [sent]
- **Data Object Collection** — a set of data objects (parallel MI marker on the data icon)
- **Data Input** — data entering the process from outside
- **Data Output** — data leaving the process to outside
- **Data Store** — persistent storage (database). Data here persists after the process ends.

---

## Collaborating Processes

**Black box pool** — internal process hidden. Only messages in/out are visible.
**White box pool** — internal process fully visible.
**Public process** — only the externally visible send/receive interactions.
**Private process** — full internal process including all internal tasks.
**Multi-instance pool** — multiple instances of the same participant (parallel MI marker on the pool).

---

# PART 5 — PROCESS CHOREOGRAPHIES

## Orchestration vs Choreography

**Orchestration** = one participant's internal process. Shows what ONE company does step by step.

**Choreography** = how MULTIPLE independent participants interact and coordinate. Shows message exchange between participants, not internal steps.

**Why this matters:** When two companies interact, if the message ORDER doesn't match, a **deadlock** occurs — Company 1 waits for a message Company 2 hasn't sent, because Company 2 is also waiting. Choreography design prevents this.

---

## The Four Phases of Choreography Development

### Phase 1 — High-level Structural Design (Participant Identification)
Identify who is involved and how they are connected. Output is a structural diagram showing participant roles and communication channels (dashed lines between boxes).

Example: Seller ↔ Auctioning Service ↔ Bidders

### Phase 2 — High-level Behavioural Design (Milestone Definition)
Define key milestones of the collaboration and the order they must be reached.

Milestones shown as circles:
- Single border = initial milestone
- Double border = intermediate milestone
- Bold border = ultimate goal (end)

Gateways between milestones show whether milestones happen in parallel (AND) or as alternatives (XOR).

Example: Auction is set up → Bidding phase is over → (Goods delivered AND Payment completed) → Auction finished successfully. OR if no bids → Auction finished unsuccessfully.

### Phase 3 — Collaboration Scenarios (Choreography Definition)
Refine milestones by introducing specific messages between participants. Shows WHICH messages cause WHICH milestones to be reached.

Example: To reach "Auction is set up" → Seller sends auction creation request → Service responds with account creation request → Seller sends registration info → Service confirms → Seller receives auction creation confirmation.

### Phase 4 — Behavioural Interfaces
For each participant, derive what messages they send and receive and in what order. This becomes the implementation contract for each participant's internal system.

---

## Service Interaction Patterns

**Send** — one participant sends a message, the other receives it. One-way.

**Receive** — one participant waits to receive a message before proceeding.

**Send/Receive** — one sends a request and waits for a response. The other receives, processes, and replies.

**One-to-Many Send** — one participant sends the same message to multiple instances of another participant. Example: send election notices to all citizens. Uses parallel MI marker.

**One-from-Many Receive** — one participant receives messages from multiple senders. Example: seller receives bids from multiple bidders. Uses event-based gateway.

**One-to-Many Send/Receive** — send a request to many and receive responses from all. Example: travel agency requests prices from multiple airlines.

**Racing Incoming Messages** — event-based gateway reacts to whichever message arrives first. Example: customer confirmation OR rejection arrives at travel agency.

**Contingent Requests** — sequentially delegating tasks to different participants one after another.

**Request With Referral** — a participant redirects the other to a third party. Example: bookstore receives order → refers customer to payment service → payment service confirms → bookstore ships.

---

## Choreography Diagrams

A BPMN choreography task is a divided rectangle:
- Top band (highlighted): initiating participant
- Middle: name of the interaction
- Bottom band: receiving participant
- Message icons show what is exchanged

**Non-enforceable choreography:** A choreography cannot be enforced if a participant cannot verify a prior step has occurred. Example: if a reseller ships goods after the customer "sends funds" to a payment org, but the reseller cannot directly verify payment — the choreography is not enforceable as modelled.

---

# PART 6 — ERP SYSTEMS

## What is ERP?

ERP (Enterprise Resource Planning) is a system that integrates all major functions of a company into one system — logistics, finance, HR, production, procurement, warehousing, sales and distribution.

Three critical perspectives for the exam:
1. **Integration Perspective** — merging logistics, finance and HR into one system
2. **Efficiency Perspective** — entrepreneurial use of resources to optimize operations
3. **Information Flow Perspective** — software facilitating data flow between functions

Primary focus: **intra-company processes** (inside the company) and integration of functional silos.

Key suppliers worldwide: SAP, Oracle, Microsoft.

**BPM models the business process. ERP implements and integrates the business process.**

---

## Evolution of ERP

- **1960s–70s** — MRP (Material Requirements Planning): production planning only
- **1980s** — MRP II: added finance and HR to production planning
- **Modern ERP** — full-scale integration of every business function

---

## Organizational Structures: Silos vs Processes

The **silo problem** is based on Michael Porter's value chain — the separation of primary from secondary functions. Each department only cares about its own goals and stops communicating with others.

The solution is shifting focus to cross-functional cooperation, which leads to IT-based integration — that is, ERP systems.

---

## ERP Technical Architecture (The Four Legs)

An ERP system needs four elements to hold up its transactions:

1. **Organizational Data** — the company structure (e.g. storage locations, plants)
2. **Master Data** — long-term data that doesn't change often (e.g. material info, vendor info, customer info)
3. **Rules** — the business logic (e.g. order value limits, approval thresholds)
4. **Transactions** — the actual work events (e.g. Purchase Order 1234)

---

# PART 7 — ERP IMPLEMENTATION

## Selection Criteria

When selecting an ERP, companies evaluate:
- **Functionality** — does it do what we need, can it be customized?
- **Technology** — does it work with current systems and devices?
- **Vendor** — what is their reputation in the industry?
- **Support** — what help is provided before and after purchase?
- **Costs** — Total Cost of Ownership (TCO): includes hardware upgrades, network costs and training, not just the software price

## Critical Success Factors (CSFs)

The top factors that determine whether an ERP implementation succeeds:

1. **Top Management Support** — leaders must be fully committed
2. **Training** — users need different types of training for their roles
3. **Project Management** — clear vision, goals and objectives
4. **Change Management** — carefully managing how employees react to the new system
5. **BPR (Business Process Re-Engineering)** — redesigning business steps to fit software best practices
6. Interdepartmental communication
7. Project champion
8. Use of ERP implementation consultants
9. Communication among implementation team members
10. End-user involvement

## The Five Implementation Steps

1. **Project Preparation** — teams organize and define the scope
2. **Business Blueprint** — documentation of what the business actually needs
3. **Realization** — configure the software in the development system
4. **Final Preparation** — testing, data migration and end-user training
5. **Go Live and Support** — turn the software on and provide help desks

## Four Go-Live Approaches

| Approach | Description | Risk |
|----------|-------------|------|
| **Big Bang** | Change everything at once | High — difficult to roll back |
| **Parallel** | Run old and new systems simultaneously | Expensive — double the work |
| **Phased** | Switch one process or department at a time | Lower — more controlled |
| **Pilot** | Switch one department first to test | Low — learn before scaling |

---

# PART 8 — PROCUREMENT

## What is Procurement?

Procurement is the act of obtaining goods and services. It includes planning, specifying, selecting suppliers, ordering, receiving and paying.

Split into two halves:

**Commercial Purchasing** (strategic) — specifying requirements, selecting suppliers, contracting, negotiating prices, service level agreements.

**Logistics Purchasing** (operational) — ordering, monitoring, goods flow management, after-sales.

## Business Goals of Procurement

1. Continuity of business processes
2. Cost reduction
3. Reducing strategic vulnerability regarding supplies
4. External representation in the supplier market
5. Product and process innovation

---

## Kraljic's Matrix (Strategic Purchasing Matrix)

A matrix to decide how to treat different products based on two dimensions: strategic risk and supply complexity.

| | Low Complexity | High Complexity |
|--|----------------|-----------------|
| **High Risk** | Leverage Products | Strategic Products |
| **Low Risk** | Normal Products | Bottleneck Products |

- **Strategic** — high risk, high complexity. Manage closely, build long-term relationships.
- **Bottleneck** — high complexity, low risk. Secure supply, keep safety stock.
- **Leverage** — high risk, low complexity. Exploit buying power, multiple suppliers.
- **Normal** — low risk, low complexity. Automate and simplify purchasing.

---

## Purchase for Stock vs Purchase for Consumption

**Purchase for stock** — goods are stored in a warehouse. Uses the material master record and updates stock accounts.

**Purchase for consumption** — goods are used immediately when received. Assigned to a cost center or asset via Account Assignment Category. No stock update.

---

## The Purchase-to-Pay Cycle (5 steps)

1. **Purchase Requisition** — internal request for materials
2. **Purchase Order** — formal legal commitment sent to the vendor
3. **Goods Receipt** — physical arrival of the goods
4. **Invoice Verification** — the system compares PO, goods receipt and vendor invoice. All three must agree (three-way match) before payment is allowed.
5. **Payment** — sending the funds to the vendor

---

# PART 9 — INVENTORY AND WAREHOUSE MANAGEMENT

## Purpose of a Warehouse

Warehouses exist for: fast order fulfillment, guaranteeing supply, reducing transport costs, economies of scale, supporting production, and as a business model (storing and distributing for others).

Stores: raw materials, semi-finished goods, finished products.

## Two Types of Warehouse

**Production Warehouse** — designed to support manufacturing, keeps raw materials and semi-finished goods close to the factory floor.

**Distribution Warehouse** — collects products from multiple suppliers and delivers to multiple customers. Can be central (covering large regions) or local (close to specific markets).

---

## Core Warehouse Functions (Physical Flow)

1. **Receipt and Inspection** — unload goods, check quality, accept or reject
2. **Put Away** — update the system inventory, move goods to the defined storage location
3. **Storage** — store using methods like pallets, racks, bins
4. **Order Picking** — the MOST EXPENSIVE process, accounting for 50% of operating costs. Collect specific quantities from different locations to fulfill customer orders.
5. **Dispatch** — check completeness and send packages out

---

## Value Added Logistics (VAL)

Modern warehouses don't just store goods — they also perform VAL activities that improve or customize products. Examples:
- Kitting: bundling items into a package
- Repackaging for specific markets
- Returns processing

---

## Layout Planning — The PQRST Key

To plan a warehouse efficiently:
- **P** — Product: what is being moved?
- **Q** — Quantity: how much?
- **R** — Route: to where?
- **S** — Supporting devices: what equipment is needed?
- **T** — Time: when does it move?

---

## IM vs WM — Two Levels of Stock Tracking in ERP

**Inventory Management (IM)** — tracks stock at a HIGH level. Knows the total quantity in a Storage Location.

**Warehouse Management (WM)** — tracks stock at a GRANULAR level. Knows exactly which storage bin or shelf an item is on.

**The link between them:**
Goods Receipt in IM → triggers a Transfer Requirement in WM → creates a Transfer Order → physically moves the item to the specific bin.

---

# PART 10 — PRODUCTION

## Bill of Materials (BOM)

A BOM is a complete, formally structured list of all components that make up a product or assembly. Like the parts list when building furniture — it tells you exactly how many of each component you need.

In ERP, the system "explodes" the BOM: if you want to build 10 cars, the system automatically calculates that you need 40 wheels, 10 engines, etc.

**LLC (Low Level Code)** — LLC 0 is always the finished product. Higher numbers are sub-assemblies and raw materials. Used to read BOM diagrams correctly.

---

## Customer Order Decoupling Point (CODP)

The CODP is the point in the production process where the product becomes linked to a specific customer order. It is the "wait point."

- **Before CODP** — make parts based on forecast (push approach, MTS)
- **After CODP** — make the final product because a specific customer has ordered it (pull approach, MTO/ETO)

---

## Calculating Production Lead Time

To find the total throughput time, identify the **longest cumulative path** through the BOM from bottom to top — not the sum of all paths.

Example: Frame takes 11 weeks, wheels take 8 weeks. The lead time is 11 weeks (not 19) because you can order wheels in parallel, but you can't finish the bike until the frame is ready.

**Common mistake: adding all lead times together instead of taking the longest single path.**

---

## MTS vs MTO vs ETO

| Strategy | Meaning | Lead time | Price | Customization |
|----------|---------|-----------|-------|---------------|
| **MTS** (Make-to-Stock) | Made before order, kept in inventory | Short | Low | Minimal |
| **MTO** (Make-to-Order) | Made after customer orders | Medium | Medium | More options |
| **ETO** (Engineer-to-Order) | Custom designed per order | Long (as quoted) | High | Full |

The CODP determines which strategy is used: if the decoupling point is at the very start, it's MTS. If it's at the very end, it's ETO.

---

## The Production Cycle in ERP (5 steps)

1. **Request Production** — triggered by a warehouse shortage or new sales order
2. **Authorize Production** — production manager creates a production order
3. **Issue Raw Materials** — warehouse physically moves parts to the factory floor
4. **Create Product** — actual manufacturing
5. **Receive Finished Product** — new items moved back to warehouse, production order closed

---

# PART 11 — FULFILLMENT (ORDER-TO-CASH)

## What is Fulfillment?

Fulfillment covers the entire journey from initial sales inquiry to final delivery and payment. The goal is the "winning promise" — balancing large assortments, low prices and end-to-end convenience for the customer.

---

## Order-to-Cash Cycle (7 steps)

1. **Pre-sales Activities** — sales inquiry starts (contact, quote, contract)
2. **Sales Order Entry** — customer request finalized in the ERP
3. **Check Credit** — accounting department verifies if the customer can pay
4. **Pick and Pack** — select items from warehouse, prepare for shipment
5. **Shipping** — physically send the goods, update stock account
6. **Billing** — send the invoice, update accounts receivable
7. **Receipt of Payment** — finalize the transaction when funds arrive

---

## Organizational Structure in Fulfillment

**Sales Organization** — responsible for sales and distribution in a specific geographical area. The highest level for sales reporting. Negotiates terms and conditions.

**Distribution Channel** — how products reach the customer (wholesale, retail, internet). Different channels may have different pricing and shipping plants.

**Division** — a product line grouping (e.g. bicycles, accessories, skateboards). May have its own pricing and customer strategy.

**Sales Area** — a unique combination of Sales Organization + Distribution Channel + Division.

**Delivering Plant** — the plant from which products or services are physically delivered to the customer.

**Shipping Point** — a specific location within a plant from which outbound deliveries are dispatched (e.g. loading dock, mailroom, rail depot).

---

## Customer Master Data (Three Levels)

1. **General Data** — name, address, internal ID. Defined at client level (shared across the company).
2. **Accounting Data** — bank info, payment terms. Defined at company code level.
3. **Sales Area Data** — pricing, shipping conditions, partner roles. Defined at sales area level.

---

## Delivery Scheduling

**Backward Scheduling** — start from the required delivery date and work backwards:
Required delivery date ← Transit time ← Loading time ← Pick/pack time ← Material availability

Used to calculate WHEN the process must start to meet the customer's requested date.

**Forward Scheduling** — if backward scheduling shows the start date has already passed, start from the earliest possible date and calculate FORWARD to give the customer a confirmed (later) delivery date.

---

## Fulfillment Enablers (How to Beat Competitors)

Large assortment, low price, availability, same-day/next-day delivery, flexible returns, real-time demand management, one-view inventory, last-mile efficiency, seamless customer experience.

---

## Order Management KPIs

**Ordering Placement:** shopping cart abandonment rate, order placement percentage, average order value.

**Fulfillment Cost:** cost per order, shipping cost per order, accuracy in order picking.

**Fulfillment Time:** order fill rate, order cycle time, on-time shipping rate, orders picked per hour.

**Post-Delivery:** rate of returns, perfect order rate, scrap rate.

---

## Transportation Planning

Goal: fulfill customer demand while maximizing resource utilization and reducing delivery cost.

**Travelling Salesman Problem (TSP)** — what is the shortest route visiting all delivery points? Brute force is impossible at scale (7 deliveries = 5,040 possibilities; 300 deliveries = unworkable). Systems use heuristics like nearest-neighbor search.

Vehicle routing constraints include: weight/volume capacity, total time, loading/unloading times, driver hours legislation, licenses, traffic, access restrictions.

Tools: CVRS (Computerized Vehicle Routing and Scheduling), fleet management, track-and-trace, e-CMR (electronic consignment note), EDI/XML interfaces.

**APS (Automatic Planning & Scheduling)** — advanced systems that support complex routing and delivery planning.

---

# COMPARISON TABLES

## EPC vs BPMN

| Feature | EPC | BPMN |
|---------|-----|------|
| Activity shape | Rounded rectangle (function) | Rounded rectangle (task/activity) |
| Event shape | Hexagon | Circle |
| Decision shape | Connector circle (XOR/AND/OR) | Diamond gateway |
| Strict alternation rule | Yes — Event→Function→Event | No strict alternation |
| Events can make decisions | No — events cannot follow XOR/OR splits | Yes — event-based gateway |
| Role assignment | RASCI lines to functions | Pools and lanes |
| Use context | Internal analysis, ARIS framework | Industry standard, implementation |

## Orchestration vs Choreography

| | Orchestration | Choreography |
|--|--------------|--------------|
| Focus | One participant's internal process | Message exchange between multiple participants |
| Shows | What one company does step by step | How participants coordinate |
| Diagram type | Standard BPMN process (single pool/lanes) | Collaboration diagram or choreography diagram |
| Risk | Doesn't show cross-company alignment | Deadlocks if message ordering is wrong |

## Gateway Summary

| Gateway | Symbol | Split | Join |
|---------|--------|-------|------|
| XOR | X | Exactly one path | First arrival |
| AND | + | All paths | Wait for all |
| OR | O | One or more | Wait for all activated |
| Event-based | ⬡ | First event wins | N/A |
| Complex | * | Custom logic | Custom logic |

---

# EXAM TRAPS — READ THESE CAREFULLY

1. **Sequence flow vs message flow** — sequence flow is INSIDE one pool, message flow CROSSES between pools. Using sequence flow between two separate companies is wrong.

2. **EPC decision rule** — events CANNOT be followed by an XOR or OR split. Only functions can make decisions. A very common exam question.

3. **EPC alternation** — you cannot have two functions in a row or two events in a row. Always Event → Function → Event.

4. **Interrupting vs non-interrupting boundary events** — solid border = activity stops; dashed border = activity continues.

5. **Event-based gateway vs XOR** — XOR decides based on a data condition evaluated NOW. Event-based gateway WAITS for an external event.

6. **Call Activity vs Subprocess** — subprocess is embedded in the diagram; call activity references a separately defined global process elsewhere.

7. **Choreography deadlock** — if Company A waits for Company B before sending, and Company B waits for Company A before sending, neither ever sends. The choreography must ensure message ordering prevents this.

8. **Transaction compensation** — when a transaction fails, you need explicit compensation activities (◄◄) to reverse completed steps. BPMN does not automatically undo anything.

9. **Production lead time** — take the LONGEST SINGLE PATH through the BOM, not the sum of all paths.

10. **Three-way match in procurement** — before payment is released, the Purchase Order, Goods Receipt and Vendor Invoice must all match.

11. **IM vs WM** — IM knows total quantity in a location. WM knows the exact bin. A goods receipt in IM triggers a transfer requirement in WM.

12. **CODP** — before the decoupling point you make to forecast, after it you make to order. The position of the CODP defines whether you are MTS, MTO or ETO.

13. **Default flow in XOR gateway** — the default path (marked /) is evaluated LAST, after all other conditions fail.

14. **Ad-hoc ≠ loop** — ad-hoc means no forced order and steps can be skipped. Loop marker means the same task repeats with a condition. Not the same thing.

15. **BPM vs BPMS** — BPM is the discipline (the science). BPMS is the tool (the software). Never confuse them.
