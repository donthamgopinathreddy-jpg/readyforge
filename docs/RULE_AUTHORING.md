# Rule authoring

A ReadyForge rule should test a failure mode that can materially affect correctness, security, privacy, accessibility, reliability or release quality.

Avoid vague rules such as `UI should look good`.

Prefer testable language such as: `Primary and destructive touch targets expose a semantic label and satisfy the platform minimum practical touch-target requirement.`

Every rule must state whether it is automatic, semi-automatic, manual or device-only, and must define evidence and acceptance criteria.
