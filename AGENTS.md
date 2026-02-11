# Quantum Mail Client  
## Implementation Objectives for Coding Agent

This project is a Windows desktop Outlook-like email client that integrates Quantum Key Distribution (QKD) with existing email infrastructure.

The system must remain compatible with standard providers such as Gmail and Yahoo.  
SMTP and IMAP must NOT be modified.

All cryptographic protection is applied at the application layer.

---

# Core Goals

1. Provide a GUI for:
   - user authentication
   - composing mail
   - inbox viewing
   - message reading
   - attachment handling

2. Communicate with a Python backend responsible for:
   - encryption and decryption
   - interaction with Key Managers
   - SMTP sending
   - IMAP retrieval

3. Obtain symmetric key material from a Key Manager using the ETSI GS QKD 014 style REST interface.

4. Support multiple selectable security levels.

5. Maintain modularity so encryption mechanisms can be upgraded or swapped without affecting email transport.

---

# Architectural Principle

Transport remains classical.  
Security enhancement comes from how keys are sourced and how messages are encrypted before transmission.

compose → encrypt → SMTP send
IMAP receive → decrypt → display

---

# Required Integrations

## Email Infrastructure
Must work with real servers such as:
- Gmail

Using:
- SMTP for sending
- IMAP for retrieval

---

## Key Manager Communication
Backend must request keys via REST endpoints modeled after ETSI GS QKD 014.

A mock KM server will be used during development.

---

# Multi-Level Security Modes (MANDATORY)

The user must be able to select the level before sending.

---

## Level 1 (L1)  
One-Time Pad using QKD keys.

Properties:
- maximum theoretical security
- key length must match data length
- highest consumption

---

## Level 2 (L2)  
Quantum-aided AES-256-GCM.

Properties:
- symmetric encryption
- QKD provides key material
- practical balance of security and performance
- expected default mode

---

## Level 3 (L3)  
Post-Quantum Cryptography (e.g., Kyber-1024).

Properties:
- independent of QKD
- future-resistant
- uses liboqs or equivalent

---

## Level 4 (L4)  
No encryption.

Used for:
- compatibility testing
- baseline comparison

---

# Backend Responsibilities

The backend must:

- fetch keys from KM
- manage key identifiers
- encrypt outgoing messages according to selected level
- embed required metadata for decryption
- send via SMTP
- retrieve via IMAP
- decrypt and reconstruct original content
- provide results back to the GUI

---

# Frontend Responsibilities

The frontend must:

- present mailbox
- allow composing
- allow attachment upload
- allow choosing security level
- request operations from backend
- display decrypted results

The frontend must NOT perform cryptographic operations.

---

# Key Requirements from Problem Statement

The application must:

- resemble a modern email client
- integrate QKD via ETSI-style APIs
- remain compatible with existing infrastructure
- be modular and upgradeable
- run on Windows

---

# Development Reality Constraint

Real quantum hardware is NOT required.

Mock or simulated QKD services are acceptable and expected.

---

# Success Condition

A message sent through Gmail or similar provider should:

- appear encrypted in standard mail clients
- become readable only after decryption through this application

---

# End of Objectives

