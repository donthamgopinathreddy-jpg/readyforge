# ReadyForge

**ReadyForge** is an open-source production-readiness framework and AI audit skill for mobile applications.

The first reference implementation targets **Flutter + Supabase + Android**.

ReadyForge is designed to answer one question:

> Is this application actually ready to ship?

It does not treat successful compilation as production readiness. It audits the complete delivery chain:

`UI → interaction → state → repository/service → RPC/Edge Function → authorization/RLS → database/storage → failure states → device E2E → store release`

## Status model

Every check resolves to one of:

- `PASS`
- `FAIL`
- `WARNING`
- `MANUAL`
- `NOT_APPLICABLE`

AI-assisted audits additionally use `FIXED` and `BLOCKED`.

A check may never be marked `PASS` merely because code was changed. Evidence and re-verification are required.

## Core audit domains

ReadyForge covers project/build health, UI completeness, 10-state UI coverage, responsive/overflow safety, accessibility, theme consistency, navigation and deep links, authentication and onboarding, authorization, backend wiring, database integrity, RLS, storage security, Edge/server functions, notifications, offline/degraded behavior, privacy and deletion, secrets/configuration, dependencies, performance, observability, Android release configuration, physical-device E2E, store readiness, release and rollback.

## 10-state UI rule

Every important data-driven screen should intentionally handle:

1. Default
2. Loading
3. Refreshing
4. Empty
5. Error
6. Retry
7. Success
8. Disabled
9. Degraded / cached
10. Offline / interrupted

Cross-cutting checks include large text, keyboard/IME, reduced motion, duplicate taps, stale data, permissions, app lifecycle, role-specific visibility, semantics and device-width safety.

## Initial architecture

```text
readyforge/
├── skills/production-auditor/SKILL.md
├── rules/
│   ├── flutter/
│   ├── supabase/
│   ├── android/
│   ├── security/
│   ├── privacy/
│   └── release/
├── schemas/
├── templates/
├── examples/cotrainr/
├── docs/
└── .github/workflows/
```

## First milestone

`v0.1` establishes the specification, AI skill, machine-readable rules, contribution contract and reference profile.

Future milestones add a Dart CLI, GitHub Action, Supabase inspection adapter, Patrol integration, SARIF/JSON/HTML output, PR comments, iOS, Firebase and React Native adapters.

## License

Apache-2.0.
