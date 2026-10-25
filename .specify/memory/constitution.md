# helloworld-speckit-java-springboot-aws Constitution

## Core Principles

### I. Simplicity First (YAGNI)
This project is a Hello World service. Every addition MUST be justified by a requirement in the
current spec. Speculative features, extra layers, and unused dependencies MUST NOT be added.
Any complexity beyond a single Spring Boot module MUST be recorded in the plan's Complexity
Tracking section with the simpler alternative that was rejected.
Rationale: the project exists to demonstrate the Speckit workflow end to end; scope creep hides
that signal.

### II. Spec-Driven Development
Work MUST follow the Speckit sequence: constitution → specify → plan → tasks → implement.
Code MUST trace to a task in `tasks.md`, and each task to a requirement in `spec.md`. When
implementation reveals that a spec or plan is wrong, the artifact MUST be amended before the code
diverges from it.
Rationale: traceability from requirement to code is the point of using Speckit.

### III. Test-First (NON-NEGOTIABLE)
Tests MUST be written before implementation and MUST be observed failing before the code that
satisfies them is written (Red-Green-Refactor). The greeting endpoint MUST have at least one
automated test at the HTTP level (e.g., Spring `MockMvc`/`WebTestClient` or
`@SpringBootTest`) asserting status and response body. The test suite MUST pass before any merge.

### IV. Twelve-Factor Spring Boot Service
The service MUST be a Spring Boot application on a current LTS Java release, built with a single
build tool (Maven or Gradle, chosen in the plan). Configuration MUST come from the environment or
Spring profiles, never hard-coded. Logs MUST go to stdout/stderr. The service MUST expose a
health endpoint (Spring Boot Actuator `/actuator/health`) for use by AWS health checks, and MUST
shut down gracefully on SIGTERM.
Rationale: these properties make the service deployable on any AWS compute target unchanged.

### V. Reproducible AWS Deployment
The deployable artifact MUST be built by an automated, repeatable command and MUST be identical
across environments. All AWS resources MUST be defined as code (e.g., CloudFormation, CDK, or
Terraform, chosen in the plan); manual console changes MUST NOT be the source of truth. Secrets
and credentials MUST NOT be committed to the repository; the service MUST use IAM roles or AWS
managed secret stores.

## Technology & Deployment Constraints

- Language/framework: Java and Spring Boot (versions pinned in the build file).
- Target platform: AWS. The specific compute service is decided in `/speckit-plan`.
- Dependencies MUST be limited to what the current spec requires; each new dependency MUST be
  justified in the plan or PR description.
- The repository MUST contain a README documenting how to build, test, run locally, and deploy.

## Development Workflow & Quality Gates

- Work happens on feature branches created through the Speckit feature workflow; `main` MUST
  stay releasable.
- A change MAY merge only when: the build succeeds, all tests pass, and the change is traceable
  to a spec requirement and task.
- Each PR MUST include a check against this constitution; violations MUST be fixed or justified
  in the plan's Complexity Tracking section.
- Commits SHOULD be small and reference the task they complete.

## Governance

This constitution supersedes other project practices. Amendments MUST be made through
`/speckit-constitution`, include a rationale, and update the version below. Versioning follows
semantic versioning: MAJOR for backward-incompatible principle removals or redefinitions, MINOR
for added principles/sections or materially expanded guidance, PATCH for clarifications and
wording. Compliance MUST be reviewed at plan time (Constitution Check) and at PR review.

**Version**: 1.0.0 | **Ratified**: 2026-10-25 | **Last Amended**: 2026-10-25
