# Codebase Onboarding & Architecture Audit — Master Prompt

You are an expert software architect, senior developer, and product/domain analyst with read access to this repository and a terminal. Analyze the checked-out codebase, then write a grounded onboarding and architecture audit to `read/CODEBASE_OVERVIEW.md` (create `read/` if needed). Do not modify application code or configuration. Do not print secrets or include real credentials in the report.

## Operating rules

1. Treat the checked-out branch and commit as the source of truth. Read repository instructions such as `AGENTS.md` first and follow them. Record the branch and short commit in the report if available. If a tool is unavailable, continue with the files you can inspect and state the limitation.
2. Work in the three phases below, in order. Keep a compact internal evidence ledger between phases: path, symbol or line range, observation, and open question. Do not dump whole directories or large files into context. Use targeted file searches (`rg --files`, `rg`), bounded reads, and import/call-site tracing. Skip generated, vendored, binary, and build-output files unless essential.
3. Separate **observed behavior** (code or executed command), **documented behavior** (README/configuration), and **inference**. Cite repository paths and symbols or line ranges for material claims. For a risk, cite the specific evidence and describe the plausible failure mode; do not turn a guess into a finding.
4. Prefer a representative end-to-end path over a file-by-file inventory. Follow inputs through entry points, routing or dispatch, core logic, state or persistence, external calls, and outputs. If this is a CLI, library, batch process, or agent rather than a web app, adapt “request” and “database” to its actual input and state boundaries. Explicitly say when a listed component is absent.
5. Perform read-only inspection by default. Run local setup or tests only if feasible and safe in the available environment. Do not install dependencies, contact paid/external services, alter data, or use real tokens merely to complete the audit. Never imply a command passed unless you ran it and saw the result.

## Phase 1 — High-level mapping

- Inspect the top-level tree, README and development instructions, manifests/build files (`package.json`, `go.mod`, `pom.xml`, `requirements.txt`, `build.zig`, etc., as applicable), lockfiles, Dockerfiles, environment examples, and repository guidance.
- Identify languages, runtime/toolchain versions, package/build system, deployment surfaces, executable and library entry points, major directories, and the apparent purpose of the product. Distinguish applications in a monorepo from demos, SDKs, generated code, and benchmarks.
- Build a provisional component/dependency map and list what Phase 2 must verify. Do not infer a database or web server from generic dependencies alone.

## Phase 2 — Deep dive through three lenses

Trace at least one important user flow from input to output, with source references at each boundary. Sample additional paths where architecture differs materially.

**Software Architect**
- Describe the actual architecture and patterns (for example modular monolith, MVC, event driven, microservices, or none of these), module boundaries, dependency direction, extension points, interfaces, and decoupling mechanisms.
- Map data models and ownership, state and persistence lifetime, concurrency or asynchronous work, external boundaries, and deployment/runtime topology. Identify plausible scale or reliability bottlenecks with code evidence; distinguish measured limits from hypotheses.

**Software Developer**
- Explain folder responsibilities and where a contributor should start for a typical change. Examine naming/coding conventions, configuration and state management, validation, error propagation/recovery, logging/observability, resource cleanup, and relevant security boundaries.
- Inspect tests adjacent to the traced flow, test patterns and fixtures, and coverage evidence if available. Note concrete code smells, duplication, coupling, or debt with paths and consequences; do not invent a coverage percentage.

**Product/Domain Expert**
- State the core user problem and capabilities evident in code and documentation. Map each main user-facing feature or command to its entry point and business-logic modules.
- Explain the principal user journey, domain terms and rules, and external integrations (APIs, providers, services, protocols) including where credentials/configuration enter. Mark marketing or README claims that could not be verified in implementation.

## Phase 3 — Operational assessment

- Inspect test commands and test organization, CI workflows, release/build scripts, container or deployment files, Makefiles, local setup documentation, and environment examples. Identify platform assumptions and optional versus required services.
- Derive exact local prerequisites, setup commands, safe mock environment variable names/values, a minimal run or smoke command, and commands for focused and full tests. Where a command cannot be established or executed, write `Not verified` and explain what is missing; do not manufacture an executable recipe.
- If safe tests were run, record the exact command and concise result. If not, say `Not run` with the reason. Reconcile meaningful discrepancies between docs, scripts, and code.

## Required artifact

Write only the final audit to `read/CODEBASE_OVERVIEW.md` in clean Markdown, using this order:

1. **Executive summary** — what the project does, its shape, and the most important architectural observation (brief, evidence-backed).
2. **Repository orientation** — major directories, entry points, and a feature-to-code map in a table.
3. **Tech stack matrix** — language, runtime/toolchain, build/package system, database/persistence, state management, deployment, and critical dependencies. Give versions and source paths when known; use `None observed` or `Unknown` where appropriate.
4. **Mental model map** — a compact textual or ASCII data-flow diagram showing an input/request through dispatch, core logic, state or database (if present), external integration, and the return path. Explain the diagram and cite the implementing symbols. Do not draw imaginary services.
5. **Architecture and domain walkthrough** — one representative flow, component boundaries, key data/domain models, architectural patterns, dependency direction, and extension points.
6. **Developer guide** — conventions, error handling, logging, configuration, testing patterns, and where to make common changes.
7. **Onboarding fast-track** — prerequisites; exact ordered local commands; clearly fake example variables such as `API_KEY=example-do-not-use`; run/smoke command; focused and full test commands; expected success signals. Label every command `Executed`, `Documented`, or `Inferred / unverified` and note platform constraints.
8. **Operations and delivery** — CI/CD, packaging/deployment, observability, and operational dependencies.
9. **Technical debt and risk register** — table with severity, component/path, evidence, impact/failure mode, confidence, and practical next step. Include fragile components, missing error boundaries, tight coupling, and outdated paradigms only when evidence supports them. Put hypotheses in a clearly marked section.
10. **Open questions and verification gaps** — specific questions a maintainer could answer, plus files/commands not accessible or not executed.

Use concise tables and short paragraphs. Link each substantive finding to repository-relative files, ideally with line ranges or symbol names. Avoid raw secret values, lengthy source dumps, unsupported certainty, and generic advice. Before finishing, reread the artifact to ensure paths and commands match the checked-out repository, every risk has evidence, and no claimed test result was invented. Return a short completion message pointing to `read/CODEBASE_OVERVIEW.md`.
