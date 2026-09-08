# ReadyForge Production Auditor Skill

## Purpose
Audit an application from first screen to production release. Do not stop at UI review, compilation, or unit tests.

## Mandatory trace
For every product area trace:

`UI → interaction → state/provider → repository/service → RPC/Edge Function → authentication/authorization → RLS/policies → database/storage → failures → device behavior → release configuration`

If a layer does not exist, classify it `NOT_APPLICABLE`. Never silently skip it.

## Allowed final states
Every finding must end as one of:

- PASS
- FIXED
- FAIL
- WARNING
- MANUAL
- BLOCKED
- NOT_APPLICABLE

A code edit alone is not evidence of a fix. Re-run the relevant check.

## Severity
- CRITICAL: cross-user access, credential exposure, arbitrary privilege escalation, destructive corruption, release-key exposure.
- HIGH: auth bypass, broken deletion, unreliable core transaction, crash in primary flow, incorrect production configuration.
- MEDIUM: degraded UX, stale state, retry failure, accessibility defect, non-critical performance issue.
- LOW: polish, consistency, maintainability issue.
- INFO: observation or future improvement.

## 10-state UI rule
For each important screen/component classify:
1. Default / normal
2. Loading / initial fetch
3. Refreshing / reloading
4. Empty / no data
5. Error / failed request
6. Retry / recovery
7. Success / completed action
8. Disabled / unavailable action
9. Partial / degraded / cached
10. Offline / interrupted

Also audit pressed/selected/focused, keyboard behavior, double taps, navigation while work is in flight, stale cache, destructive confirmation, permission denial, small viewports, large text, light/dark, reduced motion, TalkBack/semantics, touch targets, role visibility and first-use discoverability.

## Responsive rule
At minimum evaluate 320dp, 360dp, 393–412dp, short/tall screens, 2.0 text scale, keyboard/IME, system insets, gesture and 3-button navigation, long strings, dialogs/bottom sheets and supported landscape behavior.

Never hide layout defects by clamping platform text scaling.

## Backend authorization rule
Frontend visibility is not authorization. For every sensitive mutation derive actor identity server-side, verify ownership/relationship, role, account state and entitlement/subscription where applicable. Use transactions for quota/counter changes. RLS must not create a weaker alternate write path.

## RLS rule
PostgreSQL permissive policies combine with OR. Flag ownership bypass patterns such as owner policy plus separate active-account write policy, unrestricted authenticated INSERT, weak UPDATE USING clauses, and direct table writes that bypass vetted RPC/Edge Functions.

## SECURITY DEFINER rule
For every SECURITY DEFINER function pin search_path, classify intended callers, inspect grants, derive actor identity server-side, reject unintended cross-user parameters and revoke internal helpers from client roles.

## Storage rule
Inspect storage separately from table RLS. Verify bucket purpose, path ownership, read/write/delete authorization, signed URL authorization, content-type/size restrictions and deletion cleanup.

## Notification rule
Trace:
`event → DB insert/job → preference gate → dispatcher → device token → push provider → OS channel → foreground/background/killed routing → deep link/action`

Check idempotency, duplicate jobs, token rotation, cross-account token ownership, logout cleanup, server-side preferences and stale actions.

## Database integrity rule
Inspect PKs, FKs, unique/check constraints, orphan rows, duplicate logical rows, transaction boundaries, timezone assumptions, missing FK indexes, duplicate indexes, migration drift and cron/job idempotency. Never delete an index merely because an advisor labels it unused.

## Secret rule
Never commit or quote service-role keys, signing keys, OAuth client secrets, cron secrets, Firebase private keys or access/refresh tokens. If exposed: classify, rotate, move to secret management, remove from durable source where practical and verify replacement consumers.

## Authentication rule
Test email/password, enabled OAuth providers, email confirmation if enabled, password reset, restrictions, incomplete onboarding, token refresh, process death, cold/warm deep links, duplicate identities, password policy and leaked-password protection where supported.

## Navigation rule
A valid auth session alone may not imply full app access. Protected routes must consider authoritative onboarding, restriction, verification and account state. Test malformed IDs and direct route entry.

## Data truthfulness rule
Never render fabricated zero/default values when data is unavailable. Differentiate true zero, not loaded, unavailable, error and stale cached data.

## Refresh ownership rule
Avoid duplicate loading indicators and refresh chains where one failed subsystem aborts unrelated sections. Each async subsystem needs clear loading/error ownership.

## Privacy and deletion rule
Deletion must cover auth identity, first-party DB rows, shared relational rows, deliberately retained audit/legal records, storage, device tokens, third-party integrations, OAuth tokens, notification jobs, analytics/crash identity and local device state.

## Release rule
Do not declare production ready until code quality passes, release build succeeds, signing/package IDs/backend endpoints are correct, debug diagnostics are safe, critical physical-device E2E passes, privacy/legal/store declarations match behavior and rollback exists.

## Evidence rule
Every PASS must record evidence: file/function, command/test, migration/RPC/policy, device case or release artifact. Manual checks remain MANUAL until explicitly verified.
