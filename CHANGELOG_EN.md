# Changelog

All notable changes to the Shutong Dev Workflow will be documented in this file.

Format based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
versioning follows [Semantic Versioning](https://semver.org/).

---

## [1.2] - 2026-07-25

### Added

- **From-scratch project mode**: INIT stage (tech stack selection, directory structure, dependency planning, entry point design, config design, extension points)
- **Acceptance criteria in INIT** (7th question): functionality checklist, acceptance testing, performance/stability requirements
- **DEPLOY stage** (prompts/09-deploy.md): environment check, dependency install, build, deploy execution, smoke test, monitoring
- **Layer-by-layer building**: Config → Utility → Business, verify each layer before proceeding
- **Acceptance testing**: INIT defines criteria, VERIFY checks against them
- **From-scratch example session** (examples/example-session_en.md Case 2): complete "todo app" build process
- **Mode selection** (prompts/00-setup_en.md): choose "modify existing" or "build from scratch"
- **README_EN.md update**: dual-mode flow diagrams, applicable scenarios

### Modified

- 00-setup_en.md: Added mode selection step and mode B initialization supplement
- 01-probe_en.md: Added INIT branch, mode selection header, 7th question (acceptance criteria)
- 02-design_en.md: Added Branch C (from-scratch architecture design)
- 03-contract_en.md: Added Branch B (from-scratch: data model, API interface, error handling)
- 04-code_en.md: Added Branch B (from-scratch: layer-by-layer building)
- 05-verify_en.md: Added Branch B (from-scratch: acceptance criteria verification)
- 06-done_en.md: Added Branch B (from-scratch: docs completion, handover, git tag)
- README_EN.md: Updated directory structure, stage flow, audience, principles, versions
- CHANGELOG_EN.md: Added v1.2 entry

---

## [1.1] - 2026-07-24

### Added

- Git safety guide (prompts/08-git-safety_en.md)
- Exception handling hardening (prompts/07-exception-handling_en.md)
- PROBE scope limiting (prioritize 3-5 files when codebase is large)
- Execution discipline (absolute rules initialization)

---

## [1.0] - 2026-07-24

### Added

- 7-stage complete workflow: PROBE → DESIGN → CONTRACT → CODE → VERIFY → DONE
- Dual-AI architecture: Strategy AI (review direction) + Execution AI (write code)
- State judgment mechanism: A (brand new) / B (partial) / C (completed)
- Progress coordinates: `[PROBE_DONE]`, `[FILE_DONE]`, `[STEP X/N]`, `[ALL_DONE]`
- Exception handling cheat sheet: violation phrases, stuck phrases
- Complete prompt templates (prompts/ directory)
- Comparison analysis document (docs/comparison.md)
- Dual-AI design rationale (docs/why-two-ai.md)
- Full example session (examples/example-session.md)
