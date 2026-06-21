# SPECTRUM — The UK Museum Collections Management Standard

_Reference Note for MCOS — 2026-06-21_

---

## What Is SPECTRUM?

SPECTRUM is the collections management standard for museums, published by [Collections Trust](https://collectionstrust.org.uk/) (UK). It defines the procedures, information requirements, and workflows that underpin professional museum collections management. First published in 1994, it is now in version 5.1 (published September 2022) and is used by museums worldwide, not only in the UK. In the UK it is the required standard for museums seeking [Arts Council England Accreditation](https://collectionstrust.org.uk/accreditation/).

SPECTRUM is not software. It is a procedural standard — a description of *what* museums should do and *what information* they should record, which any software system (or paper-based system) can implement. Many commercial and open source collections management systems advertise SPECTRUM compliance or SPECTRUM partnership status.

---

## Structure: 21 Procedures

SPECTRUM 5.1 is organized around 21 collections management procedures. Each procedure defines the steps a museum should follow, the minimum information it must record, and the links to related procedures.

### Nine Primary Procedures

These are the core procedures that UK-accredited museums must demonstrate they follow. They are considered essential for basic collections stewardship.

| # | Procedure | What It Covers |
| :--- | :--- | :--- |
| 1 | **Object entry** | Logging all objects entering the museum's care — loans, enquiries, potential acquisitions — before any formal decision is made |
| 2 | **Acquisition and accessioning** | Taking legal ownership of objects; formal commitment by the governing body to care for objects long-term; assignment of accession numbers |
| 3 | **Location and movement control** | Recording where every object is at all times; updating the record every time an object moves |
| 4 | **Inventory** | Verifying the existence and location of objects; a check of the physical collection against the documented collection |
| 5 | **Cataloguing** | Recording and managing descriptive and contextual information about objects, from multiple perspectives, to meet user needs |
| 6 | **Object exit** | Managing the departure of objects — return of loans, outgoing loans, disposals — with proper authorization and documentation |
| 7 | **Loans in** (borrowing objects) | Formal agreements to borrow objects from other institutions or individuals, including condition, insurance, and return |
| 8 | **Loans out** (lending objects) | Formal agreements to lend objects, including condition-out/condition-in recording, insurance, and return deadlines |
| 9 | **Documentation planning** | Writing and maintaining a documentation plan that sets priorities and targets for improving collection records |

### Twelve Additional Procedures

These cover the full range of collections management activity beyond the primary nine.

| Procedure | What It Covers |
| :--- | :--- |
| **Use of collections** | Managing how objects are used — by researchers, in displays, for loans — and recording that use |
| **Condition checking and technical assessment** | Systematic assessment of object condition; recording condition grades and notes at defined points |
| **Collections care and conservation** | Preventive conservation, environmental monitoring, conservation treatment, and treatment records |
| **Valuation** | Recording the valuation of objects for insurance, probate, or other purposes |
| **Insurance and indemnity** | Managing insurance coverage for objects in the museum's care and on loan |
| **Emergency planning for collections** | Preparing for and responding to disasters, including salvage priorities |
| **Damage and loss** | Recording damage to or loss of objects in the collection, with investigation and reporting |
| **Deaccessioning and disposal** | Formally removing objects from the collection, following ethical and legal requirements |
| **Rights management** | Identifying and recording intellectual property rights in objects and associated media |
| **Reproduction** | Managing requests to reproduce images or content from the collection |
| **Collections review** | Systematic review of collections against the museum's collecting policy; informing deaccession decisions |

---

## Units of Information

SPECTRUM defines not just procedures but also the specific **units of information** that should be recorded at each step — field-level definitions that go beyond procedure names to specify exactly what data a museum needs. These units are grouped into information groups (e.g., *Object identification information*, *Object location information*, *Acquisition information*). SPECTRUM 5.1 introduced no new or changed units of information relative to 5.0, maintaining continuity for software systems.

---

## SPECTRUM and MCOS

MCOS aligns with SPECTRUM at the schema and workflow level. The alignment is not a formal certification claim — MCOS does not seek SPECTRUM Partner status — but the procedural model informed key design decisions:

| SPECTRUM Procedure | MCOS Implementation |
| :--- | :--- |
| Object entry | Object record creation before formal accession decision |
| Acquisition and accessioning | Full accession record with unique accession number, provenance, credit line, and formal date |
| Location and movement control | Hierarchical location tree (building → room → case → shelf); full movement history per object (Phase 2) |
| Inventory | Location-based object listing; physical verification workflow |
| Cataloguing | Full descriptive record: title, maker, date, medium, dimensions, description, rights statement |
| Loans in / Loans out | Multi-object loan agreements with condition-out/condition-in, insurance value, return deadlines, overdue alerts (Phase 2) |
| Condition checking | Condition grade, condition notes, and condition assessment date on every object record |
| Rights management | Rights statement required field; public publishing blocked without a declared rights statement |
| Deaccessioning and disposal | Audit trail records all record deletions with user attribution and before/after values |

---

## Further Reading

- [SPECTRUM overview — Collections Trust](https://collectionstrust.org.uk/spectrum/)
- [Introduction to SPECTRUM 5 — Collections Trust](https://collectionstrust.org.uk/spectrum/spectrum-5/)
- [All 21 procedures — Collections Trust](https://collectionstrust.org.uk/spectrum/procedures/)
- [SPECTRUM primary procedures (Accreditation context)](https://collectionstrust.org.uk/accreditation/managing-collections/collections-information/spectrum-primary-procedures/)
- [What's new in SPECTRUM 5.1?](https://collectionstrust.org.uk/spectrum/spectrum-5/summary-of-changes/)
- [SPECTRUM 4.0 full text (PDF)](https://collectionstrust.org.uk/wp-content/uploads/2016/11/spectrum_4_03.pdf) — the last freely available full-text version
