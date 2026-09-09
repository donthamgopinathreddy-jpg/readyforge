# ReadyForge Play Store Release Manager Skill

## Purpose

Drive a verified Android application from release-ready source to Google Play testing, review submission, staged production rollout, and post-release control.

This skill is an orchestrator. It must consume the ReadyForge production-auditor result and must never treat upload success as release readiness.

## Release invariant

The exact artifact that passed release gates must be the artifact promoted between tracks.

Bind every release to:
- Git commit SHA
- versionName
- versionCode
- AAB SHA-256
- package name
- upload/signing certificate identity
- ReadyForge audit result
- metadata revision
- Play edit/track/release identifiers

Do not rebuild a different artifact for promotion unless versionCode changes and the full gate is rerun.

## Required lifecycle

`AUDIT -> BUILD -> SIGN -> METADATA_VALIDATE -> INTERNAL -> DEVICE_E2E -> CLOSED/OPEN -> SUBMIT_REVIEW -> APPROVED -> STAGED_PRODUCTION -> FULL_PRODUCTION -> POST_RELEASE_VERIFY`

Allowed failure/control transitions include:
- `ANY_PRE_PRODUCTION -> BLOCKED`
- `STAGED_PRODUCTION -> HALTED`
- `HALTED -> STAGED_PRODUCTION` only after explicit approval and healthy replacement/evidence

## Google Play API workflow

Where the Android Publisher API supports the action:
1. create an app Edit
2. upload/update AAB, listings, images, testers, tracks and supported declarations
3. validate the Edit
4. commit the Edit
5. record resulting release state/evidence

Never log service-account credentials, access tokens or private signing material.

## Metadata ownership

Store release metadata in source control, for example:

```text
playstore/
  en-US/
    title.txt
    short_description.txt
    full_description.txt
    changelogs/<versionCode>.txt
  graphics/
    icon.png
    feature_graphic.png
    phone/
    tablet/
  declarations/
    data_safety.yaml
    health_apps.yaml
    account_deletion.yaml
  release.yaml
```

Before sync:
- validate required locales
- validate text limits
- validate image presence/dimensions where enforceable
- verify privacy-policy URL responds successfully
- verify account-deletion URL responds successfully when accounts exist
- verify metadata claims match implemented app behavior

A missing or contradictory policy declaration is a release blocker, not a warning.

## Build and signing

Require:
- production applicationId/package
- monotonically increasing versionCode
- release signing configured
- no debug-signing fallback
- successful release AAB build
- SHA-256 recorded
- upload key kept outside repository

The skill must refuse upload when artifact identity cannot be proven.

## Tracks

Support at minimum:
- internal
- closed testing
- open testing where selected
- production

Promotion must use configured gates.

Example:

```yaml
promotion:
  internal_to_closed:
    require_device_e2e: true
    require_zero_critical: true
    require_zero_high: true
  closed_to_production:
    require_review_ready: true
    require_explicit_approval: true
```

## Review submission

ReadyForge may prepare and commit all supported Play edits automatically once credentials and owner configuration exist.

It must distinguish:
- API-automatable changes
- Play Console one-time configuration
- human declarations/legal attestations
- Google review state

Never claim an app is "approved" merely because an Edit was committed.

## Staged rollout

Production must support configurable staged rollout.

Example:

```text
10% -> 25% -> 50% -> 100%
```

Each increase must require release-health evidence or an explicit configured approval.

Support:
- start rollout
- increase rollout fraction
- halt rollout
- resume only with explicit gate
- record rollout state

Never silently promote to 100% production.

## Policy gates

At minimum verify or mark MANUAL/BLOCKED for:
- Privacy policy
- Data safety
- Account deletion web resource when applicable
- Health apps declaration when applicable
- Health Connect permission justification when applicable
- Content rating
- Target audience
- Ads declaration
- App access/test credentials if login is required
- Sensitive/restricted permission declarations
- Country/distribution setup

Human-required Play declarations must remain `MANUAL` until completed by an authorized account owner.

## Post-release

After production rollout begins, capture:
- Play release state
- rollout fraction
- crash/ANR health if integrated
- critical backend errors if integrated
- user-impacting auth/deep-link/notification regressions if monitored

If configured health thresholds fail, recommend or execute a rollout halt only when the user's automation policy explicitly permits that action.

## Safety

Never:
- commit Google service-account JSON
- commit upload/signing private keys
- echo credentials in logs
- upload an unverified artifact
- overwrite newer Play metadata from a stale checkout
- reuse a versionCode already consumed by a different artifact
- mark manual Play declarations complete without evidence
- silently release to production

## Suggested commands

```text
readyforge play metadata validate
readyforge play metadata diff
readyforge play metadata sync
readyforge play deploy internal
readyforge play promote closed
readyforge play submit-review
readyforge play promote production --rollout 0.10
readyforge play rollout --to 0.25
readyforge play rollout --to 0.50
readyforge play rollout --to 1.00
readyforge play rollout halt
readyforge play status
```
