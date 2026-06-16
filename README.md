# MCIS — Museum Collections Information System

MCIS will be a free, open source collections management system designed for small museums, historical societies, and specialized collections that need professional-grade tools without the cost or complexity of commercial software such as PastPerfect, Argus, or The Museum System (TMS).

## Overview

MCIS separates public discovery from private operations through a two-tier architecture:

- **Public tier** — selected records and images published to the museum's Internet Archive collection for free, permanent public access
- **Private tier** — full operational data maintained in a secure, self-hosted PostgreSQL database covering collections, loans, donors, members, and inventory

A lightweight desktop client can be installed on a laptop or workstation and connect to a PostgreSQL database running on one machine the museum already owns. No web server or cloud subscription required.

## Key principles

- Free and open source — no licensing fees
- Runs on modest hardware the museum already owns
- Low training curve for volunteers and part-time staff with high turnover
- Modular — museums can adopt only the modules they need and expand over time
- Data portable — museums own their data; no vendor lock-in; standard SQL exports
- Internet Archive publishing as a first-class feature, not an afterthought
- Accessible by design — UI will meet WCAG 2.1 AA keyboard navigation and screen reader standards

## Technology stack

| Layer          | Technology                     |
| :------------- | :----------------------------- |
| Desktop client | Python / PySide6               |
| Database       | PostgreSQL                     |
| Database layer | SQLAlchemy                     |
| IA publishing  | internetarchive Python library |
| Packaging      | PyInstaller (Windows, Linux)   |

## Project status

MCIS is in early development, currently in **Phase 0 — Project Infrastructure** (schema design, repository setup, CI configuration).

For the full project plan — including module architecture, phased roadmap, entity model, competitive landscape, risks, and glossary — see [docs/MCIS_High_Level_Plan.md](docs/MCIS_High_Level_Plan.md).

## Documentation

| Document | Description |
| :------- | :---------- |
| [High Level Plan](docs/MCIS_High_Level_Plan.md) | Full project plan: architecture, phases, entity model, risks, glossary |
| [Phase 1 Schema](docs/schema_phase1.md) | Database schema — Collections, Objects, Media, Users, AuditLog |
| [Phase 2 Schema](docs/schema_phase2.md) | Database schema — Locations, Loans, Donors; Phase 1 migrations |
| [Use Cases](docs/use_cases/index.md) | 36 use cases for validating schema correctness and UI behaviour across Phases 1 and 2 |

## Getting involved

We are looking for:

- A pilot museum willing to test early releases
- Contributors familiar with Python, PySide6, or PostgreSQL
- Museum professionals who can advise on collections workflow requirements

Visit the [GitHub repository](https://github.com/juren53/MCIS) to open an issue or start a discussion.

## License

License will be MIT or GPL v3 — decision expected before the first code release. See the [project plan](docs/MCIS_High_Level_Plan.md) for the licensing rationale.
