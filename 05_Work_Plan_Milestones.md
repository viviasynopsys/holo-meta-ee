# 05 — Work Plan, Milestones and Schedule

> **Scope:** An 18-month, part-time-realistic programme taking the Meta-EE concept from an empty
> folder to a submitted journal article, on the audited single-workstation hardware.
>
> **Planning stance:** milestones are defined by **evidence produced**, never by activity performed.
> "Ran simulations" is not a milestone; "LPA error map populated across 4 deflection angles with
> convergence demonstrated" is.

---

## 1. Programme shape

Six phases, each ending in a **decision gate** at which the programme may continue, adapt, or fall
back down the ladder in doc 02 §4.5.

| Phase | Months | Theme | Exit artefact |
|---|---|---|---|
| **P0** | 0–1 | Foundation & de-risking | Toolchain proven end-to-end on a trivial case |
| **P1** | 1–4 | Nanoscale: libraries & the LPA study | **Novelty 1** secured |
| **P2** | 4–7 | Element design & CGH engine | Working Meta-EE + reconstruction |
| **P3** | 7–11 | System: Zemax + SPEOS | **Novelty 3** secured |
| **P4** | 11–14 | Tolerancing, stretch goals, robustness | Complete result set |
| **P5** | 14–18 | Writing, review, submission | Submitted manuscript |

The ordering is deliberate: **the riskiest and most novel work (P1) happens early**, while there is
still time to fall back. A plan that defers its risk to month 12 is not a plan.

---

## 2. Phase P0 — Foundation and de-risking (Months 0–1)

**Purpose:** prove the *joins* before investing in the physics. The single largest programme risk is
that a cross-tool bridge does not work as advertised; that must surface in month 1, not month 9.

| # | Task | Output | Days |
|---|---|---|---|
| P0.1 | Build `sim/` tree, provenance layer, version manifest (doc 04 §2) | `common/provenance.py` | 3 |
| P0.2 | **Licence entitlement check (constraint K8):** all three products concurrently, **plus ZOS Premium/Enterprise** (needed for the dynamic RCWA link) **and the Speos HUD Design & Analysis add-on** (needed for HOA) | Pass/fail report | 1 |
| P0.3 | `lumapi` headless smoke test; confirm `grating*` command set on a known grating | Verified notebook | 2 |
| P0.4 | Exercise the **`rcwa`** script command (documented; resolved) and benchmark RCWA vs. FDTD on one unit cell | Benchmark + decision note | 1 |
| P0.5 | **Prove `lumerical-metalens-2026R1-1.dll`** on a textbook metalens | Working `.ZMX` | 3 |
| P0.6 | **Prove `lumerical-sub-wavelength-dynamic-link-2026R1-1.dll`** live co-sim; measure call latency | Benchmark | 4 |
| P0.7 | PySpeos gRPC connection; **LSWM (`.lswm`) surface-plugin import** (primary) and a synthetic BSDF (secondary) | Working Speos surface | 4 |
| P0.8 | `MCP_VV` + `pylumerical-mcp` wired into the agent environment; evaluate the MIT `optical-automation` library | Orchestration demo | 2 |
| P0.9 | FDTD memory-scaling benchmark: patches A→D (doc 04 §4) | Measured memory/time curve | 3 |
| P0.10 | Baseline ASM propagator; **establish POP's valid low-angle domain** and agreement there (re-scoped E5, constraint K1) | Validation notebook | 2 |
| P0.11 | Reproduce the **AR Windshield HUD** KB example (`/articles/44843180268179`) as the tri-tool template | Working tri-tool baseline | 4 |

### **Milestone M0 — "Toolchain proven" (end of Month 1)**

> A single script runs: Lumerical unit cell → phase library → Zemax surface → ray trace → SPEOS BSDF
> → illuminance map, for a *trivial* test element, with full provenance.

**Gate G-P0 — continue only if:**
- [ ] All three products scriptable headlessly and concurrently licensed
- [ ] **ZOS Premium/Enterprise and Speos HUD Design & Analysis entitlements confirmed (K8)**
- [ ] At least **one** Lumerical→Zemax bridge working (metalens UDS *or* dynamic link)
- [ ] LSWM or BSDF import into SPEOS confirmed
- [ ] Patch D (30 µm) measured to fit in **< 60 GB**

**If P0.2 shows the entitlements are missing** → the dynamic link and HOA are unavailable. Fall back
to pre-tabulated RCWA and the direct-view arm (C7). **Discover this in week 1, not month 8.**
**If P0.6 fails** → fall back to pre-tabulated RCWA (doc 04 §8 fallback); add E4 to budget. *Not fatal.*
**If P0.9 shows Patch D infeasible** → reduce to Patch C (22 µm); the LPA study still stands with a
slightly weaker convergence claim. *Not fatal.*
**If P0.7 fails** → SPEOS fed only by ray-file sources rather than an LSWM/BSDF surface; reduces but
does not remove the SPEOS contribution. *Not fatal.*

---

## 3. Phase P1 — Nanoscale libraries and the LPA study (Months 1–4)

**The most important phase.** Novelty claim 1 lives or dies here.

| # | Task | Output | Weeks |
|---|---|---|---|
| P1.1 | Material models: TiO₂, SiO₂, SiN validated against literature `n,k` | Material report | 1 |
| P1.2 | L1 library: 40 radii × 3 λ × 9 angles × 2 pol (doc 04 §3) | `library_L1.h5` | 2 |
| P1.3 | Convergence study: mesh, monitor placement, PML | Convergence appendix | 1 |
| P1.4 | **Gate G1** | Signed-off library | — |
| P1.5 | L2 dispersion library (annular cells, 2 DOF) | `library_L2.h5` | 3 |
| P1.6 | **LPA study — patches A, B, C** | Partial error map | 3 |
| P1.7 | **LPA study — patch D (30 µm)** overnight runs | Full error map | 2 |
| P1.8 | LPA **correction model** + residual re-measurement | Correction model | 2 |
| P1.9 | **Gate G2** | **Novelty 1 evidence pack** | — |

### **Milestone M1 — "LPA quantified" (end of Month 4)**

> The local-periodicity approximation's error is measured as a function of deflection angle,
> wavelength and patch size; a correction model reduces mean error by ≥ 50%; convergence demonstrated
> C→D.

**This is the point at which the paper becomes publishable even if everything after it under-delivers.**
M1 alone supports a solid methods paper in *Optics Express* or *JOSA A*. Everything subsequent raises
the venue, not the viability.

---

## 4. Phase P2 — Element design and CGH engine (Months 4–7)

| # | Task | Output | Weeks |
|---|---|---|---|
| P2.1 | IFTA over realisable phases → 13×13 radius map (doc 04 §5) | Super-cell design | 2 |
| P2.2 | **Gate G3** | Uniformity ≥ 90%, η ≥ 70% | — |
| P2.3 | Rigorous super-cell verification in Lumerical | True order efficiencies | 2 |
| P2.4 | **Gate G4** — apply G2 correction if needed; iterate P2.1 | Converged design | 1 |
| P2.5 | PyTorch CGH: forward model with rigorous `t_meta` | `cgh/` module | 3 |
| P2.6 | SGD optimiser, multi-wavelength, per-colour fan-out grids | Working CGH | 2 |
| P2.7 | Time-multiplexed speckle suppression; sweep `M` | Speckle curve | 2 |
| P2.8 | **Key figure:** idealised vs. rigorous `t_meta` reconstruction | Figure + numbers | 1 |
| P2.9 | **Gate G5** | PSNR ≥ 25 dB, C ≤ 0.15 | — |

### **Milestone M2 — "Reconstruction demonstrated" (end of Month 7)**

> A full-colour image reconstructed through the rigorously modelled Meta-EE, across a ×25-expanded
> étendue, at ≥ 25 dB PSNR — and a quantified statement of how much worse it would have been using an
> idealised metasurface model.

P2.8 deserves emphasis: it is the figure that answers *"why did you need rigorous Maxwell simulation?"*
Without it, a referee may reasonably ask whether Lumerical was necessary at all.

---

## 5. Phase P3 — System integration (Months 7–11)

| # | Task | Output | Weeks |
|---|---|---|---|
| P3.1 | Zemax sequential: relay, combiner, eyebox, FOV | `.ZMX` | 3 |
| P3.2 | Metasurface via metalens UDS; POP vs. ASM (E5) | Validation | 2 |
| P3.3 | **Gate G6** | Eyebox ≥ 8 mm, FOV ≥ ±15° | — |
| P3.4 | Zemax NSC with live dynamic-link RCWA | Ghost analysis | 3 |
| P3.5 | Ghost/stray-light attribution; wedge-angle solve (Arm A) | Stray-light report | 2 |
| P3.6 | **Gate G7** | ≥ 30 dB suppression | — |
| P3.7 | BSDF construction and SPEOS import (doc 04 §9) | `.anisotropicbsdf` | 2 |
| P3.8 | **Gate G8** | Energy conservation < 2% | — |
| P3.9 | SPEOS model: windshield, ambient sun, eyebox sensors | `.scdocx` | 3 |
| P3.10 | Luminance, ambient contrast, gamut, uniformity (S9–S12) | Result set | 3 |
| P3.11 | `.lpf` stray-light forensics | Attribution table | 1 |
| P3.12 | **Gate G9** | S9–S12 at threshold | — |

### **Milestone M3 — "Maxwell to perception closed" (end of Month 11)**

> A predicted *perceived* image with luminance ≥ 15 000 cd/m², ambient contrast ≥ 3:1 at 15 klx,
> ≥ 95% sRGB gamut — traceable, with a documented error budget, back to individual nanopillar
> geometry.

This is **novelty claim 3**, and the sentence above is the paper's abstract in one line.

---

## 6. Phase P4 — Tolerancing, robustness, stretch (Months 11–14)

| # | Task | Output | Weeks |
|---|---|---|---|
| P4.1 | optiSLang DOE setup across all 10 parameters (doc 04 §11) | DOE definition | 2 |
| P4.2 | Sensitivity run; metamodel; Sobol indices | Sensitivity report | 3 |
| P4.3 | Monte-Carlo yield at threshold specs | Yield curve | 2 |
| P4.4 | **Gate G10** | CoP > 90%, yield > 80% | — |
| P4.5 | Cross-validation harness; populate E1–E7 | **Error budget table** | 2 |
| P4.6 | **Gate G11** | All E's with uncertainties | — |
| P4.7 | *Stretch (C10):* adjoint co-design via `lumopt2` | Inverse-designed variant | 3 |
| P4.8 | *Extension (C5):* polarisation-multiplexed multi-depth | Jones-matrix variant | 2 |
| P4.9 | SiN manufacturability sensitivity case | Comparison figure | 1 |

### **Milestone M4 — "Complete, tolerance-bounded result set" (end of Month 14)**

**P4.7 and P4.8 are explicitly droppable.** They are scheduled last precisely so that abandoning them
costs nothing but ambition. If P0–P3 ran late, drop them without renegotiating the milestone.

---

## 7. Phase P5 — Writing and submission (Months 14–18)

| # | Task | Output | Weeks |
|---|---|---|---|
| P5.1 | Figure production, consistent style (`09_figures/`) | Figure set | 3 |
| P5.2 | First draft per `07_Paper_Outline_and_Figures.md` | Draft v1 | 4 |
| P5.3 | Internal technical review | Review comments | 2 |
| P5.4 | Reproducibility package: code, configs, data | Public repo + DOI | 3 |
| P5.5 | Revision | Draft v2 | 2 |
| P5.6 | External friendly review | Comments | 2 |
| P5.7 | Final revision, cover letter, submission | **Submitted** | 2 |

### **Milestone M5 — "Submitted" (end of Month 18)**

---

## 8. Schedule

```
Month  1   2   3   4   5   6   7   8   9  10  11  12  13  14  15  16  17  18
      |---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
P0    ####
P1        ################
P2                        ############
P3                                    ################
P4                                                    ############
P5                                                                ################

M0    ^M0
M1                    ^M1
M2                            ^M2
M3                                            ^M3
M4                                                            ^M4
M5                                                                          ^M5
```

**Critical path:** P0.6/P0.9 → P1.2 → P1.7 (Patch D) → P2.3 → P2.5 → P3.4 → P3.9 → P4.5 → P5.2

The two genuine schedule risks on that path are **P1.7** (overnight FDTD, serialised, ~16 h per run)
and **P3.4** (live co-simulation, potentially tens of hours). Both were deliberately de-risked in P0
(tasks P0.9 and P0.6 respectively) so that a fallback can be chosen in month 1 rather than month 8.

---

## 9. Resource plan

### Compute
Single workstation (i7-13850HX / 64 GB / RTX 2000 Ada 8 GB). Total ≈ 120–200 h wall-clock of heavy
compute (doc 04 §14) spread over 18 months — **ample margin**. Discipline required: Patch D runs
alone, overnight, with nothing else on the machine.

### Software
All present and version-coherent (audit). No procurement needed. Only new software is ours:
CGH engine, LPA harness, validation layer, optional SPEOS MCP server.

### Effort
≈ 0.5 FTE average over 18 months ≈ **9 person-months**, peaking in P1 and P3.

### Data
Estimated 200–500 GB of simulation artefacts. C: has 707 GB free, D: 954 GB. Use **D:** for bulk
simulation output; keep C: for the working tree.

---

## 10. Milestone summary

| ID | Month | Title | Success criterion | Fallback if missed |
|---|---|---|---|---|
| **M0** | 1 | Toolchain proven | End-to-end trivial case with provenance | Reduce bridges; pre-tabulate |
| **M1** | 4 | LPA quantified | Error map + correction model, converged | Smaller patch; weaker convergence claim |
| **M2** | 7 | Reconstruction demonstrated | ≥ 25 dB PSNR through rigorous model | Reduce N; single-λ; field-sequential |
| **M3** | 11 | Maxwell→perception closed | S9–S12 at threshold, error budget | Direct-view arm instead of HUD |
| **M4** | 14 | Tolerance-bounded results | Yield > 80%, E1–E7 populated | Drop stretch goals |
| **M5** | 18 | Submitted | Manuscript + reproducibility package | Lower-tier venue |

---

## 11. Go / no-go logic

| At | Continue if | Otherwise |
|---|---|---|
| M0 | ≥ 1 Lumerical→Zemax bridge works **and** Patch D fits | Re-scope to a two-tool study; re-plan |
| M1 | LPA error map shows **measurable, structured** angle dependence | Pivot to element-design paper; novelty 1 becomes a section |
| M2 | PSNR ≥ 20 dB (threshold) | Reduce to characterisation-only paper (still viable) |
| M3 | SPEOS chain closed | Publish M1+M2 as a two-tool paper |
| M4 | — | Drop stretch goals, proceed to write-up |

> **The programme is designed so that every exit point still produces a paper.** That is the single
> most important property of this plan, and it is why the riskiest work is scheduled first.
