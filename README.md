# 9_Research — Metasurface Holographic Display Research Programme

Planning and brainstorming set for a research article on a **metasurface/DOE holographic display**,
simulated end-to-end with **Ansys Lumerical + Zemax OpticStudio + SPEOS**.

**Start here → [00 — Executive Summary](00_Executive_Summary.md)**

---

## The proposal in three sentences

A holographic display fails on one number: a 3.74 µm-pitch phase SLM delivers **1.846 mm²·sr** of
étendue where a usable display needs **29.76 mm²·sr** — a **~16× deficit**. A static metasurface
étendue expander closes it, but every published expander is designed with an **idealised thin-element
model** that ignores the true angular, spectral and polarisation response of real nanostructures.
**This programme measures what that idealisation costs** — from Maxwell's equations through to what a
human actually sees in sunlight — using all three tools, each for the scale only it can reach.

---

## Documents

| # | Document | Contents |
|---|---|---|
| **00** | [Executive Summary](00_Executive_Summary.md) | ⭐ **Read first.** The whole proposal in two pages |
| 01 | [Capability Audit](01_Capability_Audit_Installed_Stack.md) | What is installed — verified on disk. Interop DLLs, APIs, hardware ceiling |
| 02 | [Concept Brainstorm & Down-Select](02_Concept_Brainstorm_and_Downselect.md) | Étendue physics; 12 concepts scored; the prior-art correction |
| 03 | [System Architecture](03_System_Architecture_HoloMeta.md) | Specifications, first-principles derivation, photometric budget, hard tool constraints |
| 04 | [Simulation Workflow](04_Simulation_Workflow_Tri_Tool.md) | 11 stages, gates G0–G11, code sketches, compute budget |
| 05 | [Work Plan & Milestones](05_Work_Plan_Milestones.md) | 6 phases, 18 months, schedule, go/no-go logic |
| 06 | [Competitive Benchmark](06_Competitive_Benchmark_BigTech.md) | Samsung, Sony, Meta, Stanford, POSTECH, Princeton; where we are alone |
| 07 | [Paper Outline & Figures](07_Paper_Outline_and_Figures.md) | Manuscript structure, 12 figures, pre-answered reviewer objections |
| 08 | [Automation & MCP Orchestration](08_Automation_MCP_Orchestration.md) | Driving the three tools from LLM agents; the SPEOS server gap |
| 09 | [Risk Register & Validation](09_Risk_Register_and_Validation.md) | 25 risks, 5-layer validation protocol, falsifiability criteria |
| 10 | [References & Bibliography](10_References_Bibliography.md) | 38 citations, Ansys example inventory, attribution corrections |
| — | [Session Log](session.md) | 📝 **Running log** — updated every session. Status, corrections register, open items |

**Suggested reading order:** 00 → 02 → 03 → 05. Read 01 for the evidence, 04 when starting work,
06–07 when writing, 08–09 throughout.

---

## Verified environment

| | |
|---|---|
| **Ansys** | 2026 R1 (`v261`) — Lumerical, Zemax OpticStudio, SPEOS, OpticsLauncher, SPEOS_HPC, optiSLang, all version-coherent |
| **Hardware** | i7-13850HX (20C/28T) · **63.7 GB RAM** · RTX 2000 Ada 8 GB (CC 8.9) · 1.6 TB free |
| **Key bridges found on disk** | `lumerical-metalens-2026R1-1.dll` · **`lumerical-sub-wavelength-dynamic-link-2026R1-1.dll`** (live RCWA co-sim) · `srg_*_RCWA` · `hologram_kogelnik.dll` |
| **MCP servers available** | `MCP_VV` (Zemax, 39 tools, verified) · `pylumerical-mcp` (Lumerical) · **SPEOS: none — gap** |

---

## The three novelty claims

The device concept is **not** the contribution — that is prior art
(Wang et al., *Opt. Lett.* 2026; Tseng et al., *Nat. Commun.* 2024; Kuo et al., SIGGRAPH 2020).

1. **The physical-realisability gap, quantified** — what idealised expander models actually cost.
2. **LPA validity mapped and corrected** at the steep phase gradients étendue expansion demands.
3. **The first Maxwell→perception pipeline** with a measured error budget at all seven tool handoffs.

---

## Five things not to forget

1. **The étendue deficit is ~16×.** Every design decision follows from it.
2. **Lead with rigour, never with "étendue expander"** — referees who know Wang 2026 will stop reading.
3. **64 GB caps FDTD at ~30 µm — which is exactly the LPA reference size.** Constraint → contribution.
4. **SPEOS must never be asked to diffract.** It is incoherent ray tracing. Highest scientific risk; answered by design.
5. **Zemax POP is invalid beyond ~20° half-angle and the design sits at ±20.4°.** Our own ASM is primary.

---

*Planning documents only — no simulation code yet. Implementation begins at Phase P0
([05](05_Work_Plan_Milestones.md) §2).*
