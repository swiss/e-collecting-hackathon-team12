# 12) Federated e-collection framework that respects cantons sovereignty while giving citizens a seamless and privacy first experience.
## https://e-initiative.ch/

## Approach

A **federated, user-centered e-collecting system** where each canton keeps full sovereignty over its own validation process.
The platform connects **distributed** canton systems through standardized checksum exchanges, not centralized data.
Validation pathways adapt to local capabilities for evolution or changes.
Citizens experience a unified, privacy-first interface; cantons retain operational independence.

## Team needed:
- Canton and communes validation experiance
- Canton IT interoperability expertise 
- Data Security / cryptography
- Backend/API integration
- Frontend
- dev-ops
- e-id implementation


## Documentation and Diagrams

<img width="791" height="628" alt="image" src="https://github.com/user-attachments/assets/205eacf9-6c72-45a1-82c5-23d5450072ca" />


<img width="1603" height="841" alt="image" src="https://github.com/user-attachments/assets/08d4860d-8777-43bb-9c45-3452c26322d8" />



### Flowchart:

#### Data Flow:

```mermaid
flowchart TD

    %% === INTAKE & PREPROCESSING ===
    A1["Digital signature submitted via app"]
    A2["Paper signatures uploaded by committee (scanned)"]

    %% --- Raw Data ---
    subgraph RAW["Table 1: raw_signatures (temporary)"]
        R1["Store digital raw data"]
        R2["Store scanned paper data temporarily"]
        R3["Generate checksum for each signature"]
        R4["Send checksum to deduplication process"]
        R5["Delete paper scan immediately after checksum creation"]
    end

    A1 --> R1 --> R3
    A2 --> R2 --> R3 --> R4 --> R5

    %% --- Deduplication & Hashing ---
    subgraph HASH["Table 2: signature_hashes (deduplication + audit)"]
        H1["Store citizen checksum + signature hash"]
        H2["Check for duplicate signatures"]
        H3["Forward to Validation Dispatcher"]
    end

    R3 --> H1 --> H2 --> H3

    %% === VALIDATION DISPATCHER === 
    H3 --> D1["Validation Dispatcher\nSelects pathway by canton policy"]

    %% === PATHWAY 1: AUTOMATED VALIDATION ===
    subgraph P1["Pathway 1 – Automated Canton Validation"]
        direction TB
        P1A["Select canton connector:\nREST API / SQL / Custom"]
        P1B["Connector authenticates with canton system"]
        P1C["Send citizen checksum or token for lookup"]
        P1D["Canton validates against registry"]
        P1E["Receive result: accepted / rejected / invalid"]
        P1F["Backend updates Tables 1 & 2"]
        P1G["Raw data deleted from Table 1"]
    end

    D1 -->|"If canton supports automation"| P1
    P1A --> P1B --> P1C --> P1D --> P1E --> P1F --> P1G

    %% --- Custom Connector Examples (Mermaid-safe) ---
    subgraph P1EX["Examples of Pathway 1 Connectors (Custom per Canton)"]
        direction TB
        CHTTP["🌐 HTTP API Example:\nPOST /api/verifySignature\nBody: {tokenHash, citizenChecksum}\n→ Response: status=valid"]
        CSQL["🗄️ SQL Query Example:\nSELECT status FROM canton_signatures\nWHERE token_hash = ? AND citizen_checksum = ?;"]
        CPOWER["📊 PowerQuery Example (Excel):\nSource = Excel.Workbook(File.Contents('Canton.xlsx'))\nTable.SelectRows(Source, each [Checksum] = tokenHash)"]
        COTHER["📂 SFTP / Batch Example:\n1. Export daily CSV to /outbox\n2. Canton returns validated CSV in /inbox\n3. Backend updates statuses"]
    end

    P1C -->|"Depending on canton tech stack"| P1EX

    %% === PATHWAY 2: MANUAL VALIDATION ===
    subgraph P2["Pathway 2 – Manual Canton Review"]
        direction TB
        P2A["Dispatcher assigns records by postcode"]
        P2B["Validator logs into portal (Validator UI)"]
        P2C["Views pending records (from Table 1)"]
        P2D["Compares name, address, and signature"]
        P2E["Approves or rejects manually"]
        P2F["Backend updates Tables 1 & 2"]
        P2G["Raw data deleted from Table 1"]
    end

    D1 -->|"If manual review required"| P2
    P2A --> P2B --> P2C --> P2D --> P2E --> P2F --> P2G

    %% === PATHWAY 3: FEDERATED E-ID ===
    subgraph P3["Pathway 3 – Federated e-ID / Token Verification"]
        direction TB
        P3A["Citizen authenticates via IdP (SwissID / eIDAS / Canton e-ID)"]
        P3B["IdP returns signed identity token (JWT/SAML)"]
        P3C["Backend verifies token signature & extracts CitizenID"]
        P3D["Compute checksum & mark as pre-validated"]
        P3E["Store checksum + token hash in Table 2"]
        P3F["Delete raw identity data from Table 1 immediately"]
    end

    D1 -->|"If canton uses federated IdP"| P3
    P3A --> P3B --> P3C --> P3D --> P3E --> P3F

    %% === POST VALIDATION ===
    P1G --> POST
    P2G --> POST
    P3F --> POST

    subgraph POST["Post-Validation & Aggregation"]
        RVAL["Update Table 2: validated = true"]
        RAGG["Aggregate validated counts per canton"]
        RPUB["Store counts in Table 3: signature_counts"]
        RLOG["Log event in SIEM / audit ledger"]
    end

    POST --> RVAL --> RAGG --> RPUB --> RLOG

    %% === RETENTION ===
    subgraph RET["Data Retention Policy"]
        RT1["Table 1: raw_signatures → deleted after validation\n(also after checksum for paper)"]
        RT2["Table 2: signature_hashes → kept until initiative closure (+ grace period)"]
        RT3["Table 3: signature_counts → permanent (public data)"]
    end

    RLOG --> RT1
    RLOG --> RT2
    RLOG --> RT3


```

#### Users Experiances flow:

*A step-by-step illustration showing how data and requests are exchanged between actors (customer, delivery site, restaurant, infrastructure), and key software components in the order process.*

```mermaid
TBD
```

## Topics addressed

*Explain how you addressed the topics presented in the [guidelines](https://www.bk.admin.ch/bk/de/home/politische-rechte/e-collecting/aktuelles.html), filling in the template below.*


| Topic | (How) is it addressed? |
| ------- | ------------------------ |
| 1     |                        |
| 2     |                        |
| 3     |                        |
| ...   |                        |

## Key Strenghts and Weaknesses

### Strengths:

- **Federated and sovereign:** Each canton keeps control of its own validation process.
- **User-focused:** Clear and simple experience for citizens across all cantons.
- **Privacy-first:** Only anonymized checksums are exchanged and kept after validation; personal data is never centralized.
- **Flexible setup:** Works with canton systems via API, SQL, PowerQuery, or e-ID.
- **Adaptable:** Can grow or simplify depending on each canton’s setup.

### Weaknesses and Mitigations:

- **Coordination needed:** Different canton systems mean extra setup and communication.
  → *Mitigation:* Use shared connector templates and a clear onboarding process.
- **Integration work:** Some cantons will need custom or manual tools.
  → *Mitigation:* Offer open-source connectors and optional tech support.
- **Manual delays:** Validation can take longer in some cantons.
  → *Mitigation:* Add alerts and support tools to notify and assist departments when manual validation is late


## Getting Started

first demo available at [https://e-initiative.ch/](https://e-initiative.ch/)


## Team Members

- SAN / @P6g9YHK6
- Yannick Rüfenacht / @yruefenacht 
- Alfa Diop-Wicki / AlfaDiop-Wicki
- Lionel Stürmer

- ...
