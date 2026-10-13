# Record architecture decisions

- **Status**: Accepted
- **Date**: 2026-10-13

## Context

Tandem will involve decisions that are hard to reverse and easy to forget: how events are queued, how conflicts
are resolved, how connectors are abstracted. Code shows *what* was built, but now *why* alternatives were rejected.

## Decision

I will record record significant architecture decisions as short Architecture Decision Records (ADRs) in `docs/adr`,
one file per decision, named `NNNN-short-title.md` with a four digit sequence number.

Each ADR contains: status, date, context, decision and consequences. An ADR is written when the decision is made,
not in advance. Accepted ADRs are not rewritten; a changed decision gets a new ADR that supersedes the old one.

## Consequences

- The reasoning behind the design stays visible to future contributors, including future me.
- There is a small cost per decision (a few minutes of writing).
- Trivial or easily reversible choices do not need an ADR.