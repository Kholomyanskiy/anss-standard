# Changelog

All notable changes to ANSS Standard will be documented in this file.

## [1.4] — 2026-07-13

### Changed
- License changed from CC BY-NC-SA 4.0 to CC BY 4.0: free for any use including
  commercial, attribution required. Specs written with ANSS carry no license obligations.
- Detail levels unified across all documents: CORE / EXTENDED / ENTERPRISE
  (previously the Standard used MINIMAL / STANDARD / FULL)
- Invariant format unified to four fields: Name / Cannot / Reason / Check —
  in the Standard, the Core Template, and all examples in Section 2.5
- Single canonical agent reading order defined in the layer-markup section;
  Sections 2.1, 6.4 and 15.2 now reference it (previously three conflicting orders)
- Section 6.3: structured outputs / JSON schema stated as the primary determinism
  mechanism; temperature guidance kept with a note that some newer models ignore it
- README: clarified that the Core Template and the full Standard use different
  section numbering (Agent Review = Section 14 in Template, 15.6 in Standard)

### Fixed
- Core Template (v1.1): agent instruction reading order aligned with the canonical
  order; EN invariant field "Verification" renamed to "Check"

## [1.3.2] — 2026-06-02

### Added
- `examples/` folder with two real-world filled specifications
- Example: Solo Founder OS (local Node.js web app with AI agent team)
- Example: LombardPro (multi-branch pawnshop SaaS)

### Improved
- README.md: shorter, added ASCII visuals for layers, workflow, and levels
- README.ru.md: Russian version brought to the same structure

## [1.3] — 2026-06

### Added
- Section 2.5: INVARIANTS — absolute constraints for agents
- Section 2.6: Architectural Principles — decision compass
- Section 5.7: Architecture Fitness Functions
- Section 5.5: Decision Log (alongside ADR)
- Section 15.6: Agent Review Specification
- Section 16: Change Specification format
- Section 1.5: Economics and ROI calculation
- Layer marking [D/E/A] for all sections
- ANSS-Core-Template: working template for 80% of projects

### Improved
- Observability split into requirements (4.5) and tools (8.6)
- Fail-Fast conditions now include YAGNI violation check
- Smoke tests now include invariant verification

## [1.2] — 2026-06

### Added
- Three-layer structure [D] Domain / [E] Engineering / [A] Agent
- Tailored Checklist Generator in agent instructions
- Section 8.10: Disaster Recovery
- Section 8.8: FinOps and cost governance
- Section 4.6: Performance Budget (frontend + AI inference)
- Supply Chain Security (SBOM, SLSA)

## [1.1] — 2026-06

### Added
- Observability requirements with Error Budget
- Performance Budget section
- Supply Chain Security
- AI monitoring in production

## [1.0] — 2026-06

### Added
- Initial release of ANSS Standard
- 17 sections covering full project lifecycle
- AI-First section (SDD methodology)
- Agent instructions with classification system
