# Changelog

All notable changes to the Shutong Dev Workflow will be documented in this file.

Format based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
versioning follows [Semantic Versioning](https://semver.org/).

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
