# MiniERP

MiniERP is a managerial mini-ERP for small retail and distribution businesses that buy, hold stock and sell, including sales on credit. It is an open-source MIT project and a learning lab for Oracle, PL/SQL, APEX, ORDS and AI-assisted development.

Status: **M0 Foundation in progress, not runnable yet**

## Architecture

The target architecture is database-centric:

Oracle AI Database 26ai Free -> PL/SQL packages -> views -> ORDS -> Oracle APEX

Docker Compose, SQLcl, utPLSQL, migrations and GitHub Actions will support the development workflow as they are introduced by later tasks.

## MVP scope

The MVP covers purchasing, inventory, sales and sales on credit for a small retail/distribution business. Business rules belong in the database layer, with APEX as the primary user interface.

## Non-goals

Brazilian fiscal compliance, including NF-e, SPED and tax calculation, is outside the MVP. POS, bank reconciliation, multi-warehouse operations and lot/serial tracking are also non-goals.

## How to run

Coming in M0. The repository is not runnable yet.

## Project guidance

- [Project Constitution](docs/constitution.md)
- [Instructions for AI agents](AGENTS.md)
- [MIT License](LICENSE)
