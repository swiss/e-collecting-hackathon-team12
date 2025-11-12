# 12) Federated e-collection framework that respects cantons sovereignty while giving citizens a seamless and privacy first experience.

## https://e-initiative.ch/

<img width="256" height="280" alt="image" src="https://github.com/user-attachments/assets/d3585d63-7d90-4f81-8ee5-6daa8572da61" />

## Approach

A **federated, user-centered e-collecting system** where each municipality keeps full sovereignty over its own validation process.
Citizens experience a unified and simple, privacy-first interface; municipalities retain operational independence.

The platform encrypts personal information with keys provided by the municipalities. This way, this sensitive data is only ever readable by the municipality that needs it.
Validation pathways adapt to local capabilities for evolution or changes.

## Documentation and Diagrams

<img width="1603" height="841" alt="image" src="https://github.com/user-attachments/assets/08d4860d-8777-43bb-9c45-3452c26322d8" />

### Registering New Municipalities

```mermaid
sequenceDiagram
    participant M as Municipality
    participant P as Platform
    participant DB as Database

    M ->> P: Send public key
    P ->> DB: Store public key
```

### Signing a petition

<img width="621" height="621" alt="Untitled Diagram drawio" src="https://github.com/user-attachments/assets/5ff664fd-4a4f-4646-9c4b-363fc62f021f" />

```mermaid
sequenceDiagram
    participant App as Swiyu App
    participant U as User
    participant P as Platform
    participant DB as Database
    participant M as Municipality

    Note over U,P: Petition signing request

    U->>P: Request to sign petition (with postal code)
    P->>DB: Check municipality registration by postal code

    alt Municipality not registered
        P-->>U: Municipality is not registered
    else Municipality registered
        P->>App: Send verification request
        App->>U: Show verification prompt
        U->>App: Approve (cryptographic signature)
        App->>P: Send signature + name + date of birth
        P->>P: Encrypt data with municipality's public key (PK_M)
        P->>DB: Store encrypted signature data
    end

    Note over P,M: Final step (initiative completion)
    P->>M: Send all signatures for the initiative
    M->>M: Decrypt signatures using private key (SK_M)
```

## Topics addressed


| Topic                                       | (How) is it addressed?                                                                                                             |
| --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| 1:  From willingness to signature           | Unified, secure user flow — e-ID login and encrypted communication & data storage.                                                |
| 2:  Access to collected signatures          | Personal information can only be decrypted by the municipality that needs them.                                                    |
| 3:  Attribution to committees               | Referral code for each collecting party.                                                                                           |
| 4:  Information on e-collecting projects    | The tool-administrator approves the content of the initiative. Registration for newsletters is not the responsibility of the tool. |
| 5:  Exclusion of invalid signatures         | Validation through e-ID.                                                                                                           |
| 6:  Prevention of lost or hidden signatures | Is in the responsibility of the municipality. Referral codes allows committees to check the numbers.                               |
| 7:  Protection of voting secrecy            | Only the municipality has access on this information.                                                                              |
| 8:  Integration with paper process          | CVS export to integrate in existing systems, openness to adapting APIs.                                                            |
| 9:  Easy adoption for municipalities        | User friendly - download functionality (CSV, PDF).                                                                                 |
| 10:  Applicable to all federal levels       | Cantonal and federal Initiative are supported, Municipal ones can be added.                                                        |

## Key Strenghts and Weaknesses

### Strengths:

- **Federated and sovereign:** Each municipality keeps control of its own validation processes.
- **User-focused:** Clear and simple experience for all citizens and municipalities.
- **Privacy-first:** All personal information is stored encrypted and only decryptable by the municipality that needs it.
- **Easy to integrate:** Can be used by the municipalities who are registered without much change to existing processes.

### Weaknesses and Mitigations:

- No validation if municipalities actually received and processed signatures.
  → *Mitigation:* Refinement of the cryptographic protocols.

## Getting Started

first demo available at [https://e-initiative.ch/](https://e-initiative.ch/)

## Team Members

- SAN / @P6g9YHK6
- Yannick Rüfenacht / @yruefenacht
- Alfa Diop-Wicki / AlfaDiop-Wicki
- Lionel Stürmer / @lionelsemion

# Changelog

## 01.11.25

- Added RSA encryption for data security.
  - Private keys distributed to communes.
  - Pub key assigned in organisation section.
- Introduced an additional signing method.
- Split organisations and communes into distinct entities.
- Secured auditing contexts based on given rsa key.
- Added referral codes to all users for collection attribution.
- Numerous UI refinements and small tweaks.

## 03.11.25

- Moved demo encryption handling to browser level:
  - Public key sent to browser.
  - Public key displayed during signing.
- Allowed empty keys for audit/export demo. (will probably be back to this to add random placeholder stuff)
- Provided graph's data as tables for screen reader compatibility.
- Implemented a guided tour for each page.
- Updated status notices to reflect progress a little more accurately.
- Added HTML captions for accessibility.
- Extended approval process to include all initiative detail changes (not just publication).
- Visually separated communes, committees, and admin orgs in the org panel.
- Fixed initiative editor bugs
- Implemented cleaner split of signing options per commune for context-specific configurations, now the process start with either asking for what commune to show supported option per commune or paper path
- added support for Municipal Initiatives

# TODO

- some SVG are broken.
- move Lang selector on first visit there should not be such a thing as "default language"
- ui teaks

## Security & Encryption

- Create a public key registry page for transparency
- Allow commune to save private key in app if wanted ? separated vault for automated key exchange?
- Transparency report on each initiative page that is public and searchable.

## Features & Workflow

- Enable URL linking support throughout the app it's a pain to be a single url page for navigation but good for prototyping quickly.
- Add auditing to export, import, and consultation tabs.
- initiative progress is clean on the front but not the back need to fix this.

## Structure & Data

- Integrate commune–postal code mapping database so we are not fitering stuff by post code but ref to this DB.
  - have it linked to onboarded organisation ?
