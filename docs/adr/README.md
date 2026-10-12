# Architecture Decision Records

Architecture Decision Records (ADRs) capture significant technical and architectural decisions, including their context, alternatives and consequences. The repository is the source of truth for these decisions.

## When to create an ADR

Create an ADR before or together with a change that affects architecture, a chosen technology, a significant integration, a durable data or deployment decision, or a trade-off that future contributors need to understand.

Do not create ADRs for routine implementation details or decisions that are already fully determined by the Constitution.

## Naming and numbering

Use a monotonically increasing four-digit prefix and a short kebab-case title:

`NNNN-short-title.md`

Start with the next available number. Do not reuse numbers, even if an ADR is superseded.

## Status

- **Proposed** — under discussion and not yet binding.
- **Accepted** — the decision is current and binding unless replaced.
- **Superseded** — replaced by a later ADR; link to the replacing ADR.

Use [template.md](template.md) for new ADRs.
