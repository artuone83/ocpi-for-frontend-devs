# OCPI - Open Charge Point Interface - 2.3.0 for Frontend Developers

## Table of Contents

- [OCPI - Open Charge Point Interface - 2.3.0 for Frontend Developers](#ocpi---open-charge-point-interface---230-for-frontend-developers)
  - [Table of Contents](#table-of-contents)
  - [Course Map](#course-map)
  - [1. EV Charging Ecosystem](#1-ev-charging-ecosystem)
    - [1.1 High-Level Architecture](#11-high-level-architecture)
  - [1.2 CPO - Charge Point Operator](#12-cpo---charge-point-operator)
  - [1.3 eMSP - e-Mobility Service Provider](#13-emsp---e-mobility-service-provider)
  - [1.4 CPMS - Charge Point Management System](#14-cpms---charge-point-management-system)
  - [1.5 EVSE - Electric Vehicle Supply Equipment](#15-evse---electric-vehicle-supply-equipment)
  - [2. OCPP - Open Charge Point Protocol vs OCPI - Open Charge Point Interface](#2-ocpp---open-charge-point-protocol-vs-ocpi---open-charge-point-interface)
    - [2.1 OCPP - Open Charge Point Protocol](#21-ocpp---open-charge-point-protocol)
  - [2.2 OCPI - Open Charge Point Interface](#22-ocpi---open-charge-point-interface)
  - [2.3 The Big Picture](#23-the-big-picture)
  - [2.4 Frontend Takeaway](#24-frontend-takeaway)
  - [3. OCPI 2.3.0 Architecture](#3-ocpi-230-architecture)
  - [4. Locations](#4-locations)
    - [4.1 Entity Hierarchy](#41-entity-hierarchy)
    - [4.2 Frontend Domain Model](#42-frontend-domain-model)
    - [4.3 Important Question: What Does "Available" Mean?](#43-important-question-what-does-available-mean)
  - [5. Tariffs \& Pricing](#5-tariffs--pricing)
    - [5.1 Frontend Takeaway](#51-frontend-takeaway)
  - [6. Tokens \& Authorization](#6-tokens--authorization)
  - [7. Sessions \& Charging Lifecycle](#7-sessions--charging-lifecycle)
    - [7.1 Frontend Example](#71-frontend-example)
  - [8. CDRs - Charge Detail Record](#8-cdrs---charge-detail-record)
  - [9. Commands, Reservations \& Charging Profiles](#9-commands-reservations--charging-profiles)
    - [9.1 Command Flow](#91-command-flow)
    - [9.2 Important: Accepted ≠ Completed](#92-important-accepted--completed)
  - [10. Production OCPI - Open Charge Point Interface](#10-production-ocpi---open-charge-point-interface)
    - [10.1 Pull](#101-pull)
    - [10.2 Push](#102-push)
    - [10.3 Why This Matters to Frontend Developers](#103-why-this-matters-to-frontend-developers)
  - [11. Data Flow You Should Understand](#11-data-flow-you-should-understand)
  - [12. The Four Most Important OCPI Relationships](#12-the-four-most-important-ocpi-relationships)
  - [12.1 Infrastructure](#121-infrastructure)
    - [12.2 Charging](#122-charging)
    - [12.3 Pricing](#123-pricing)
    - [12.4 Remote Actions](#124-remote-actions)
  - [13. Priority: What Should You Learn?](#13-priority-what-should-you-learn)
    - [Tier 1 — Know Extremely Well](#tier-1--know-extremely-well)
    - [Tier 2 — Know Well](#tier-2--know-well)
    - [Tier 3 — Learn When Needed](#tier-3--learn-when-needed)
  - [14. Practical Exercise](#14-practical-exercise)
  - [15. Recommended Learning Order](#15-recommended-learning-order)
  - [16. Quick Reference](#16-quick-reference)
    - [Core entities](#core-entities)
    - [Core protocols](#core-protocols)
    - [Core relationships](#core-relationships)
  - [17. Official Resources](#17-official-resources)
  - [Final Mental Model](#final-mental-model)

A practical learning guide for frontend developers working on EV-charging projects using **OCPI 2.3.0**.

The goal is not to memorize the OCPI specification. The goal is to understand **why the protocol exists, how the data relates to each other, how the backend interacts with OCPI, and what that means for the frontend**.

---

## Course Map

| Lesson | Topic                                           | Frontend Relevance |
| ------ | ----------------------------------------------- | ------------------ |
| 1      | EV Charging Ecosystem                           | ⭐⭐⭐⭐⭐         |
| 2      | OCPP vs OCPI                                    | ⭐⭐⭐⭐⭐         |
| 3      | OCPI 2.3.0 Architecture                         | ⭐⭐⭐⭐           |
| 4      | Locations → EVSEs → Connectors                  | ⭐⭐⭐⭐⭐         |
| 5      | Tariffs & Pricing                               | ⭐⭐⭐⭐⭐         |
| 6      | Tokens & Authorization                          | ⭐⭐⭐⭐           |
| 7      | Sessions & Charging Lifecycle                   | ⭐⭐⭐⭐⭐         |
| 8      | CDRs - Charge Detail Record & Charging History  | ⭐⭐⭐⭐           |
| 9      | Commands, Reservations & Charging Profiles      | ⭐⭐⭐⭐⭐         |
| 10     | Production OCPI: Sync, Push, Errors & Debugging | ⭐⭐⭐⭐⭐         |

---

## 1. EV Charging Ecosystem

Before learning OCPI, understand the main actors in the EV-charging ecosystem.

### 1.1 High-Level Architecture

```text
                         Roaming Network
                               │
                               │ OCPI
                               │
                 ┌─────────────┴─────────────┐
                 │                           │
                CPO                         eMSP
                 │                           │
                CPMS                        App
                 │
                OCPP
                 │
          Charging Station
                 │
                 │
                 EV
```

## 1.2 CPO - Charge Point Operator

The organization operating charging infrastructure.

The CPO generally knows:

- where charging stations are
- EVSE status
- connectors
- charging sessions
- energy consumed
- tariffs
- charging station availability

## 1.3 eMSP - e-Mobility Service Provider

The organization providing the EV charging service to the driver.

The `eMSP` might provide:

- charging station discovery
- starting charging
- stopping charging
- pricing information
- charging history
- payment

A simplified flow:

```text
Driver
  ↓
Mobile App
  ↓
eMSP
```

## 1.4 CPMS - Charge Point Management System

The backend system used by a CPO to manage charging infrastructure.

It commonly manages:

```text
CPMS
 ├── Charging stations
 ├── EVSEs
 ├── Connectors
 ├── Sessions
 ├── Transactions
 └── OCPP
```

## 1.5 EVSE - Electric Vehicle Supply Equipment

An EVSE is not the same thing as a connector.

A simplified hierarchy is:

```text
Location
   │
   └── EVSE
         │
         ├── Connector
         ├── Connector
         └── Connector
```

Think of the entities as:

- **Location** — physical place where charging infrastructure exists
- **EVSE** — independently controllable charging unit
- **Connector** — physical interface used to connect the vehicle

This distinction is important when working with OCPI data.

---

## 2. OCPP - Open Charge Point Protocol vs OCPI - Open Charge Point Interface

This distinction is fundamental.

### 2.1 OCPP - Open Charge Point Protocol

OCPP is primarily concerned with communication between a charging station and its management system.

```text
Charging Station
       ↕
      OCPP
       ↕
      CPMS
```

OCPP deals with things such as:

- starting/stopping charging
- connector status
- meter values
- station configuration
- hardware-related communication

## 2.2 OCPI - Open Charge Point Interface

OCPI is primarily concerned with communication between charging-network/business systems.

```text
CPO
 ↕
eMSP
```

or:

```text
CPO
 ↕
Roaming Hub
 ↕
eMSP
```

OCPI deals with things such as:

- locations
- tariffs
- tokens
- sessions
- CDRs - Charge Detail Record
- commands
- reservations
- charging profiles

## 2.3 The Big Picture

```text
                         OCPI
              ┌──────────────────────┐
              │                      │
             CPO ◄────────────────► eMSP
              │                      │
              │                      │
             OCPP                   App
              │                      │
              ▼                      ▼
       Charging Station            Driver
```

## 2.4 Frontend Takeaway

If you're building a CPO dashboard, your frontend may consume data that originally came from a charging station through a chain such as:

```text
Charging Station
       ↓
     OCPP - Open Charge Point Protocol
       ↓
     CPMS - Charge Point Management System
       ↓
     OCPI - Open Charge Point Interface
       ↓
   Your Backend
       ↓
    React App
```

This is why a simple frontend property such as:

```tsx
<StatusChip status={evse.status} />
```

can represent a much larger protocol flow.

---

## 3. OCPI 2.3.0 Architecture

The official OCPI specification should be your source of truth:

- [OCPI - github](https://github.com/ocpi/ocpi)

OCPI is organized into modules.

Conceptually:

```text
OCPI 2.3.0
│
├── Versions
├── Credentials
│
├── Locations
├── Tariffs
├── Tokens
├── Sessions
├── CDRs - Charge Detail Record
│
├── Commands
├── Reservations
│
└── Charging Profiles
```

Do not think of these modules as completely independent.

They are related.

For example:

```text
Location
   ↓
EVSE
   ↓
Connector
   ↓
Session
   ↓
CDR - Charge Detail Record
```

Another relationship:

```text
Location
   ↓
Tariff
   ↓
Session
   ↓
CDR - Charge Detail Record
```

---

## 4. Locations

Locations are one of the most important OCPI modules for frontend developers.

### 4.1 Entity Hierarchy

The basic hierarchy is:

```text
Location
  │
  └── EVSE
        │
        └── Connector
```

For example:

```text
Location
├── id: "LOC-123"
├── name: "Example Shopping Centre"
├── address: "Example Street 1"
│
└── evses[]
      │
      ├── EVSE
      │    ├── uid: "EVSE-1"
      │    ├── status: AVAILABLE
      │    │
      │    └── connectors[]
      │          ├── CCS
      │          └── Type 2
      │
      └── EVSE
           ├── uid: "EVSE-2"
           └── status: CHARGING
```

### 4.2 Frontend Domain Model

A frontend application might represent the data as:

```ts
type Location = {
  id: string;
  name: string;
  address: string;
  evses: Evse[];
};

type Evse = {
  uid: string;
  status: EvseStatus;
  connectors: Connector[];
};

type Connector = {
  id: string;
  standard: ConnectorType;
  power: number;
};
```

The UI could then be structured as:

```tsx
<LocationCard>
  <LocationName />
  <Address />

  <EvseList>
    <EvseCard>
      <Status />
      <ConnectorList />
    </EvseCard>
  </EvseList>
</LocationCard>
```

### 4.3 Important Question: What Does "Available" Mean?

When the frontend displays:

```text
Available
```

you need to understand exactly what the underlying status represents.

An EVSE status is not necessarily equivalent to:

> "The entire location is available."

The hierarchy matters:

```text
Location
   │
   ├── EVSE 1 → AVAILABLE
   │     ├── Connector A → AVAILABLE
   │     └── Connector B → OCCUPIED
   │
   └── EVSE 2 → CHARGING
```

This distinction can affect:

- badges
- filters
- maps
- availability indicators
- search
- booking flows
- business logic

---

## 5. Tariffs & Pricing

A tariff describes how charging is priced.

It is not necessarily as simple as:

```text
€0.50 / kWh
```

Charging prices can involve multiple components:

```text
Energy
+
Time
+
Parking
+
Flat fee
+
Restrictions
```

Conceptually:

```text
Tariff
 └── TariffElement
       ├── PriceComponent
       │     ├── ENERGY
       │     ├── TIME
       │     ├── PARKING_TIME
       │     └── FLAT
       │
       └── Restrictions
```

A user-facing tariff could potentially look like:

```text
Charging
€0.45 / kWh

Parking
€0.10 / min after 30 min

Session fee
€1.00
```

### 5.1 Frontend Takeaway

Do not assume the backend provides a single formatted price string.

OCPI provides structured pricing information.

Your application may need to transform:

```ts
Tariff;
```

into:

```ts
DisplayTariff;
```

For example:

```ts
const mapTariffToDisplayData = (tariff: Tariff): DisplayTariff => {
  // ...
};
```

This is a good candidate for a domain mapper when the frontend representation differs from the API representation.

---

## 6. Tokens & Authorization

Tokens represent authorization information used for charging.

A token can be associated with mechanisms such as:

- RFID
- mobile application
- other authorization mechanisms

The general flow is:

```text
Driver
   ↓
Authorization credential
   ↓
eMSP
   ↓
CPO
   ↓
Charging session
```

It is important to distinguish:

```text
"Can this driver charge?"
```

from:

```text
"Is this EVSE available?"
```

These are different concepts.

For example:

```text
EVSE: AVAILABLE
Authorization: Not authorized
```

versus:

```text
EVSE: AVAILABLE
Authorization: Authorized
```

---

## 7. Sessions & Charging Lifecycle

Sessions are one of the most important OCPI concepts.

Think about a real charging session:

```text
Driver
  │
  │ starts charging
  ▼
Session starts
  │
  ├── energy increases
  ├── duration increases
  ├── state changes
  │
  ▼
Session ends
  │
  ▼
CDR - Charge Detail Record - generated
```

A session can contain information such as:

```text
id
start_date_time
end_date_time
kwh
location_id
evse_uid
connector_id
currency
status
total_cost
```

The exact OCPI 2.3.0 schema should always be treated as the source of truth.

### 7.1 Frontend Example

A React application might have:

```ts
const { data: session } = useQuery({
  queryKey: ["session", sessionId],
  queryFn: () => getSession(sessionId),
});
```

But the session is not necessarily static.

Its data can change while charging is in progress.

You may therefore need mechanisms such as:

```ts
refetchInterval: 10_000;
```

or another server-driven update mechanism.

This leads to an important architectural question:

> How does the backend receive changes to OCPI resources?

That depends on the integration and is important to understand before designing frontend data-refresh behavior.

---

## 8. CDRs - Charge Detail Record

- `CDR` = `Charge Detail Record`

Think of a CDR as the finalized record of a charging session.

```text
Session
   │
   │ charging
   │
   ▼
Session finished
   │
   ▼
CDR
```

A useful mental model:

```text
Session = charging activity

CDR = finalized charging record
```

CDRs are particularly relevant to:

- billing
- invoices
- charging history
- reporting
- reconciliation

A frontend might display:

```text
Charging History

Date        Energy      Duration       Cost
------------------------------------------------
Aug 13      24.5 kWh    42 min         €12.35
Aug 10      31.2 kWh    58 min         €15.80
```

The important distinction is that a session represents the charging activity, while the CDR represents the finalized charging record.

---

## 9. Commands, Reservations & Charging Profiles

Commands are where the frontend becomes an active participant in the charging flow.

### 9.1 Command Flow

Conceptually:

```text
User
 ↓
React
 ↓
Your Backend
 ↓
OCPI Command
 ↓
CPO
 ↓
EVSE
```

Examples of operations include:

- starting a session
- stopping a session
- unlocking a connector
- reservations

A React mutation might look conceptually like:

```ts
const { mutate: stopSession } = useMutation({
  mutationFn: stopCharging,
});
```

Then:

```tsx
<Button onClick={() => stopSession(sessionId)}>Stop charging</Button>
```

### 9.2 Important: Accepted ≠ Completed

This is a critical concept for frontend state management.

The HTTP request completing does not necessarily mean the physical action has completed.

For example:

```text
Frontend
   │
   │ stop
   ▼
Backend
   │
   │ OCPI command
   ▼
CPO
   │
   │ command processing
   ▼
EVSE
   │
   ▼
Actual charging stops
```

The API response may only mean:

> The command was accepted.

The actual EVSE state may change later.

Therefore, avoid blindly assuming:

```ts
onSuccess(() => {
  setCharging(false);
});
```

Instead, determine what the API response means and how the actual state transition becomes visible to your application.

---

## 10. Production OCPI - Open Charge Point Interface

Understanding the specification is only part of working with OCPI.

In production, you also need to understand:

- pull vs push
- synchronization
- pagination
- data freshness
- asynchronous operations
- error handling
- retries
- idempotency
- state transitions
- interoperability
- debugging

---

### 10.1 Pull

Your system requests data from another system:

```text
Our Backend ───── GET ─────► CPO
```

For example:

```http
GET /locations
```

Your backend explicitly requests the information.

---

### 10.2 Push

Another system sends data to your system:

```text
CPO ───── POST ─────► Our Backend
```

This means your system may receive changes without explicitly requesting them.

---

### 10.3 Why This Matters to Frontend Developers

Your React application should not assume:

```text
GET /locations
```

is always how the underlying data changes.

A realistic architecture might be:

```text
               External CPO
                    │
                    │ OCPI
                    ▼
              Your Backend
                    │
          ┌─────────┴─────────┐
          │                   │
       Database          Application
          │                   │
          └─────────┬─────────┘
                    │
                  REST
                    │
                    ▼
                React App
                    │
             TanStack Query
                    │
                    ▼
                    UI
```

The frontend usually interacts with **your application's API**, not directly with the external OCPI participant.

---

## 11. Data Flow You Should Understand

When working on a frontend feature, try to trace the data through the entire system.

For example:

```text
OCPI
  ↓
External CPO
  ↓
Your Backend
  ↓
Backend DTO
  ↓
Frontend API Client
  ↓
TanStack Query
  ↓
Frontend DTO / Type
  ↓
Domain Mapper
  ↓
React Component
  ↓
UI
```

When debugging a frontend issue, ask:

1. Is this value defined by OCPI?
2. Did the external CPO provide it?
3. Did our backend transform it?
4. Is the frontend DTO accurate?
5. Is there a domain mapping layer?
6. Is the UI displaying the correct interpretation?
7. Could the data be stale?
8. What system is the source of truth?

---

## 12. The Four Most Important OCPI Relationships

If you don't have time to learn every OCPI feature deeply, understand these relationships extremely well.

## 12.1 Infrastructure

```text
Location
   ↓
EVSE
   ↓
Connector
```

This describes **where charging happens**.

---

### 12.2 Charging

```text
Token
   ↓
Authorization
   ↓
Session
```

This describes **who is allowed to charge and the charging activity**.

---

### 12.3 Pricing

```text
Tariff
   ↓
Session
   ↓
Cost
   ↓
CDR - Charge Detail Record
```

This describes **how charging is priced and recorded**.

---

### 12.4 Remote Actions

```text
Frontend Action
       ↓
Your Backend
       ↓
OCPI Command
       ↓
CPO
       ↓
EVSE
       ↓
State Change
       ↓
Backend Update
       ↓
Frontend
```

This describes **how user actions can eventually affect charging infrastructure**.

---

## 13. Priority: What Should You Learn?

Not every part of OCPI deserves equal attention for a frontend developer.

### Tier 1 — Know Extremely Well

```text
CPO - Charge Point Operator
eMSP - e-Mobility Service Provider
CPMS - Charge Point Management System
EVSE - Electric Vehicle Supply Equipment
Connector

Location
EVSE
Connector

Session
CDR - Charge Detail Record

Tariff

OCPI vs OCPP
```

### Tier 2 — Know Well

```text
Credentials
Tokens
Commands
Reservations
Pagination
Push vs Pull
Roles
Version discovery
```

### Tier 3 — Learn When Needed

```text
Charging Profiles
Advanced roaming scenarios
Complex tariff restrictions
Interoperability edge cases
```

---

## 14. Practical Exercise

The best way to learn OCPI in an existing project is to trace real API flows.

Pick one endpoint from your project, for example:

```http
GET /locations
```

Trace it all the way through:

```text
OCPI Specification
       ↓
OCPI Endpoint
       ↓
Backend Controller
       ↓
Backend DTO
       ↓
Frontend API Client
       ↓
TanStack Query
       ↓
Frontend DTO / Type
       ↓
Mapper
       ↓
React Component
       ↓
UI
```

Then repeat the exercise for:

```http
GET /sessions
```

and a command endpoint:

```http
POST /commands/...
```

If you can confidently trace these three flows, you will understand a large portion of what matters for frontend development on an OCPI-based system.

---

## 15. Recommended Learning Order

Don't read the entire specification cover-to-cover.

Use this sequence:

```text
                    ┌───────────────────┐
                    │ EV Ecosystem      │
                    └─────────┬─────────┘
                              ↓
                    ┌───────────────────┐
                    │ OCPI Concepts     │
                    └─────────┬─────────┘
                              ↓
                    ┌───────────────────┐
                    │ Locations         │
                    └─────────┬─────────┘
                              ↓
                    ┌───────────────────┐
                    │ Tariffs           │
                    └─────────┬─────────┘
                              ↓
                    ┌───────────────────┐
                    │ Tokens            │
                    └─────────┬─────────┘
                              ↓
                    ┌───────────────────┐
                    │ Sessions          │
                    └─────────┬─────────┘
                              ↓
                    ┌───────────────────┐
                    │ CDRs              │ Charge Detail Record
                    └─────────┬─────────┘
                              ↓
                    ┌───────────────────┐
                    │ Commands          │
                    └─────────┬─────────┘
                              ↓
                    ┌───────────────────┐
                    │ Production /      │
                    │ Integration       │
                    └───────────────────┘
```

---

## 16. Quick Reference

### Core entities

| Entity    | Meaning                           |
| --------- | --------------------------------- |
| CPO       | Charge Point Operator             |
| eMSP      | e-Mobility Service Provider       |
| CPMS      | Charge Point Management System    |
| EVSE      | Electric Vehicle Supply Equipment |
| Connector | Physical charging interface       |
| Location  | Physical charging location        |
| Token     | Authorization identity            |
| Session   | Charging activity                 |
| CDR       | Finalized charging record         |
| Tariff    | Pricing structure                 |

### Core protocols

| Protocol | Main purpose                                           |
| -------- | ------------------------------------------------------ |
| OCPP     | Charging station ↔ management system                   |
| OCPI     | Charging ecosystem / business system ↔ business system |

### Core relationships

```text
Location
  └── EVSE
       └── Connector
```

```text
Token
  └── Authorization
       └── Session
```

```text
Tariff
  └── Session
       └── Cost
            └── CDR
```

```text
Frontend
  └── Backend
       └── OCPI
            └── CPO
                 └── EVSE
```

---

## 17. Official Resources

Use the official OCPI specification as the source of truth for the exact OCPI 2.3.0 schema, fields, endpoints, and behavior.

- [OCPI GitHub repository:](https://github.com/ocpi/ocpi)
- [OCPI specification:](https://github.com/ocpi/ocpi/tree/master/releases/2.3.0)
- [Ampeco - The Complete OCPI Guide:](https://www.ampeco.com/guides/the-complete-ocpi-guide/)

When project documentation or implementation behavior conflicts with your assumptions, verify against:

1. Your project's actual OCPI version
2. Your backend implementation
3. The external CPO/eMSP integration documentation
4. The official OCPI 2.3.0 specification

---

## Final Mental Model

As a frontend developer, keep this picture in your head:

```text
                         OCPI
              ┌──────────────────────┐
              │                      │
             CPO ◄────────────────► eMSP
              │
              │
             OCPP
              │
              ▼
       Charging Station
```

And the core data relationships:

```text
Location
   ↓
EVSE
   ↓
Connector
   ↓
Session
   ↓
CDR
```

```text
Tariff
   ↓
Session
   ↓
Cost
   ↓
CDR
```

```text
Token
   ↓
Authorization
   ↓
Session
```

```text
Frontend
   ↓
Your Backend
   ↓
OCPI
   ↓
CPO / eMSP
   ↓
Charging Infrastructure
```

Once these relationships are clear, individual OCPI fields and DTOs become much easier to understand. The goal is to understand **the domain and lifecycle first, and the schema second**.
