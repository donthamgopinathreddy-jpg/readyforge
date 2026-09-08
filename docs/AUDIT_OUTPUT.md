# Audit output contract

A ReadyForge audit should return:

- project metadata
- timestamp
- commit/ref
- profile used
- domain scores
- each rule result
- evidence
- remediation
- manual verification list
- blockers
- overall readiness

Suggested exit codes:

- `0`: release gate passed
- `1`: release gate failed
- `2`: audit configuration invalid
- `3`: required external system unavailable

A PASS without evidence is invalid.
