# Project Constitution

Status: Accepted. Changes require an ADR.

## 1. Purpose and scope
MiniERP is a managerial mini-ERP for small retail/distribution businesses that buy, hold stock and sell (including sales on credit). It is a public portfolio project and a learning lab for Oracle, PL/SQL, APEX, ORDS and AI-assisted development.
Non-goals for the MVP: Brazilian fiscal compliance (NF-e, SPED, tax calculation), POS, bank reconciliation, multi-warehouse, lot/serial tracking.

## 2. Source of truth
- The repository is the source of truth. Chats are not. Important decisions, findings and results must be recorded in the repo.
- Architecture decisions are recorded as ADRs in docs/adr/. Behaviour is specified in specs/. Operational knowledge lives in docs/runbooks/.
- Before changing architecture or behaviour, read the relevant docs. If a task needs an architectural change, stop and report it; do not decide alone.

## 3. Architecture principles
- Database-centric. Oracle AI Database 26ai Free is the system of record.
- Data invariants live in constraints (PK, FK, UNIQUE, CHECK, NOT NULL).
- State transitions and multi-table business operations live in PL/SQL packages. Every business write goes through a package.
- Views serve reads and reports. ORDS exposes packages as REST without business logic. APEX is presentation only: no business rules in APEX validations or processes.
- Domain code must not depend on APEX. Packages read identity from a session context (SYS_CONTEXT, via a security package), never from APEX session state.
- Authentication does not imply authorization: an authenticated user without an active record and roles in the application model is denied.
- Stock and financial balances are derived from append-only ledgers, never edited directly.
- Money uses NUMBER with explicit precision/scale; never floating point.
- Every business table carries company_id. The MVP supports one company per user but must not prevent future multi-company membership.
- Do not add complexity before it is needed.

## 4. Security and secrets
- This repository is public. Never commit passwords, tokens, keys, real credentials, personal data, Oracle proprietary artifacts or unnecessary binaries.
- .env is never versioned; .env.example is, with placeholders only. Development credentials are clearly marked as development-only.

## 5. Reproducibility
- Never use floating tags such as latest. Pin image tags (and digests where suitable) and tool versions.
- Environments must be reproducible from the repository with documented commands. Database files live in Docker named volumes, never bind-mounted from Windows folders.
- Downloaded third-party artifacts (for example the APEX distribution) are fetched by setup scripts with pinned versions and checksums into git-ignored caches.

## 6. Quality
- Tests are part of the implementation, not a later phase. Acceptance criteria must be verifiable.
- Database logic is tested with utPLSQL; migrations are tested from scratch and as upgrades.
- Definition of Done (default): acceptance criteria met, automated tests passing, documentation updated, ADR written if a significant decision was made, no secrets, small coherent commits.

## 7. Workflow
- Spec-driven: spec -> small tasks -> implementation -> tests -> review. One task = one clear scope.
- Stop and report (do not decide) when: an architectural change is needed, a chosen technology may need to be abandoned, there is data-loss risk, a sensitive credential is required, scope grows significantly, there is a relevant Oracle/APEX/ORDS incompatibility, or two alternatives have significant trade-offs.
- Simple operational problems are solved autonomously.
- Git: trunk-based with short-lived branches. Conventional Commits (feat, fix, docs, test, chore, refactor, build, ci) with optional scope, and trailers Spec: and Task: when applicable. Small, coherent commits; no unrelated changes in one commit.

## 8. Tooling and language
- Repository language: English (code, database object names, commits, docs). Brazilian domain terms are kept in the glossary.
- Developer scripts are written in PowerShell 7 (pwsh) and used both locally and in CI. Shell scripts are used only inside containers when required.
