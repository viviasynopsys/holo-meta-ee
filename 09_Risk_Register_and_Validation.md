# 09 — Risk Register and Validation Protocol

> **Purpose:** Name what can go wrong, quantify it, and pre-commit to the response — before the
> programme starts, while judgement is still uncorrupted by sunk cost.
>
> **Stance:** a risk register written after the fact is an excuse. Written beforehand, it is a
> decision instrument. Every entry below has a **pre-agreed trigger and response**.

---

## 1. Risk scoring

Probability (P) and Impact (I) on 1–5; Severity = P × I.

| Severity | Band | Action |
|---|---|---|
| 16–25 | **Critical** | Active mitigation now; de-risk in P0 |
| 9–15 | **High** | Mitigation planned; monitored at each gate |
| 4–8 | **Medium** | Contingency documented |
| 1–3 | **Low** | Accept |

---

## 2. Technical risks

| ID | Risk | P | I | Sev | Mitigation | Trigger → Response |
|---|---|---|---|---|---|---|
| **T1** | **LPA error turns out to be negligible at all angles** — novelty 1 evaporates | 2 | 5 | **10** | Physics says coupling must grow with phase gradient; sample deliberately steep gradients up to ±30° | If `E_LPA < 3%` everywhere at M1 → *that is still a publishable negative result* ("LPA validated to X° for the first time"). Promote novelty 2 to lead |
| **T2** | **30 µm patch (D) exceeds 64 GB** | 3 | 4 | **12** | Benchmarked in **P0.9**, month 1 | Fall back to patch C (22 µm); state convergence limitation explicitly |
| **T3** | **Dynamic-link co-simulation unusably slow** | 3 | 3 | **9** | Latency benchmarked in **P0.6** | Pre-tabulate RCWA efficiency vs. (angle, λ); add interpolation error as **E4** |
| **T4** | Metasurface UDS DLL behaves unexpectedly / undocumented conventions | 3 | 4 | **12** | Proven on a textbook metalens in **P0.5**; `us_grate.c` source available as a template for a custom DLL | Write our own UDS against the shipped C template |
| **T5** | **Chromatic fan-out spread (7.0°/8.2°/9.8°)** degrades full colour beyond compensation | 3 | 4 | **12** | Per-colour CGH grids (D5); L2 dispersion cells; field-sequential fallback | Field-sequential colour; reduced simultaneity claim |
| **T6** | Fan-out uniformity target (≥90%) unreachable with realisable phases | 3 | 3 | **9** | Optimise over realisable set from the start (D7); relax N from 5→4 | Accept ×16 expansion; restate S4 |
| **T7** | Speckle contrast ≤0.15 needs impractically many sub-frames | 3 | 3 | **9** | Sweep `M`; add modest source bandwidth | Report honestly as a cost; drop real-time claim (already excluded, doc 02 §4.4) |
| **T8** | **SPEOS BSDF import loses too much information** | 2 | 4 | **8** | Proven in **P0.7**; bin-convergence study (E7) | Ray-file source path instead of BSDF |
| **T9** | Zemax POP disagrees with our ASM propagator | 2 | 3 | **6** | Matched test case in **P0.10** | Diagnose sampling/guard-band settings; use ASM as primary, POP as cross-check |
| **T10** | `lumopt2` adjoint design fails to converge (stretch C10) | 4 | 1 | **4** | Scheduled last (P4.7), explicitly droppable | Drop. No milestone depends on it |
| **T11** | Licence contention across three concurrently driven products | 3 | 3 | **9** | Tested **P0.2**; serialise stages; queue with retry | Serial execution; extend P3 |
| **T12** | Material model (TiO₂ `n,k`) disagrees with literature | 2 | 3 | **6** | Validated in P1.1 against published data | Use measured literature dispersion; report sensitivity via optiSLang |
| **T13** | Photometric budget fails to close in practice | 2 | 4 | **8** | 4× margin designed in (doc 03 §5) | Increase laser power; higher-efficiency combiner/HOE |
| **T14** | Aspect ratio 10:1 judged unmanufacturable by referees | 2 | 2 | **4** | Within demonstrated ALD TiO₂ practice; SiN case (P4.9) | Present SiN variant; soften fabrication claim |

### 2.1 The three critical technical watch items

**T2, T4, T5** all carry severity 12. All three are deliberately de-risked in **P0 (month 1)** —
before any substantial investment. This is the central scheduling decision of the programme.

---

## 3. Scientific and publication risks

| ID | Risk | P | I | Sev | Mitigation |
|---|---|---|---|---|---|
| **S1** | **Scooped** — another group publishes metasurface étendue expansion first | 3 | 4 | **12** | Move fast on M1; the *methodology* + perception-level validation is far harder to duplicate than the device. Monitor arXiv monthly |
| **S2** | Referees reject simulation-only work | 3 | 4 | **12** | Frame as a **methods** paper; rigorous cross-validation (E1–E7); manufacturable design rules; state "no fabrication" plainly (doc 02 §4.4) |
| **S3** | "Why not just use one tool?" | 2 | 3 | **6** | The étendue argument (doc 02 §1) shows each scale needs different physics; figure P2.8 quantifies what rigorous modelling actually buys |
| **S4** | **"SPEOS can't do diffraction — your pipeline is unphysical"** | 3 | 5 | **15** | **Pre-empted in the paper itself.** Dedicated subsection: phase is deliberately discarded at E6; SPEOS is asked only incoherent questions; energy conservation demonstrated at G8 |
| **S5** | Perceived as derivative of Meta's Nature 2024 metasurface-waveguide work | 3 | 4 | **12** | Different architecture (free-space expander vs. waveguide); explicitly cited as benchmark, not competitor (doc 02 §3.1) |
| **S6** | Reproducibility challenged (commercial tools) | 2 | 3 | **6** | Full driver + config + data release; version manifest; honest statement on non-redistributable vendor components (doc 08 §6) |

**S4 is the single highest-severity risk in the programme (15).** It is also entirely foreseeable and
entirely answerable — which is why doc 03 §7 and doc 04 §9 treat the coherent→incoherent handoff as a
*designed domain change* with an energy-conservation gate, rather than glossing over it. The paper
must address it explicitly and early, not in response to a referee.

---

## 4. Programme risks

| ID | Risk | P | I | Sev | Mitigation |
|---|---|---|---|---|---|
| **P1** | Schedule slip from part-time effort | 4 | 3 | **12** | Milestone-gated; every exit point yields a paper (doc 05 §11) |
| **P2** | Hardware failure / data loss | 2 | 5 | **10** | Bulk output on **D:**; versioned backup of `src/` and `configs/`; artefacts regenerable from sidecars |
| **P3** | Ansys version upgrade breaks pipeline mid-study | 3 | 3 | **9** | **Pin to `v261`**; do not upgrade during the study; record build dates |
| **P4** | Scope creep (C5, C10 extensions absorbing the schedule) | 4 | 3 | **12** | Both scheduled last and explicitly droppable (doc 05 §6) |
| **P5** | Losing the thread across an 18-month part-time programme | 3 | 3 | **9** | This document set; decision register (doc 03 §9); gate artefacts |

---

## 5. Validation protocol

Validation is layered. Each layer catches a different class of error, and no single layer is trusted
alone.

### Layer 1 — Solver self-consistency (within one tool)

| Check | Method | Criterion |
|---|---|---|
| Mesh convergence | Refine until phase change < λ/50 | Stage 1 |
| Boundary adequacy | Vary PML layers, monitor distance | < 1% change |
| Energy conservation | `Σ R + Σ T + A = 1` | < 1% |
| Reciprocity | Swap source/monitor | < 2% |
| Time-step / apodisation | Vary FDTD run length | Converged spectrum |

### Layer 2 — Cross-solver (same physics, different method)

| Pair | Case | Criterion |
|---|---|---|
| FDTD ↔ STACK (`stackrt`) | Unstructured multilayer | **< 2%** (E1) |
| FDTD ↔ RCWA | Same unit cell | **< 3%** |
| Zemax POP ↔ our ASM | Identical propagation | **< 1%** (E5) |
| Zemax NSC ↔ SPEOS | Matched **incoherent** case | **< 5%** |

### Layer 3 — Approximation error (the scientific core)

| Approximation | Reference | Deliverable |
|---|---|---|
| **Local periodicity (LPA)** | **30 µm full-wave FDTD** | **Error map + correction model (Novelty 1)** |
| Periodic tiling / finite size | Patch-size convergence A→D | < 3% (E3) |
| Phase-library interpolation | Dense vs. sparse sampling | < 2% (E4) |
| BSDF angular quantisation | Bin-count convergence | < 5% (E7) |

### Layer 4 — Analytic and limiting cases

| Check | Expectation |
|---|---|
| Grating equation | Order angles match `sin θ_m = mλ/Λ` exactly |
| Scalar diffraction limit | Recovered for shallow, low-NA gratings |
| Étendue conservation | `E_out ≤ E_in × N²`, never exceeded |
| Photometric budget | Simulated luminance within 20% of doc 03 §5 hand calculation |
| Zero-phase element | Reduces to a plain substrate |

### Layer 5 — End-to-end consistency

1. **Energy audit** — total power conserved source→eyebox within **3%**.
2. **Redundant path** — irradiance via Zemax NSC and via SPEOS agree within **5%**.
3. **Ablation** — idealised vs. rigorous `t_meta` (figure P2.8): the difference must be *explainable*,
   not merely observed.

---

## 6. Falsifiability

Stated up front, because a study that cannot fail is not a study. **The core claims are wrong if:**

- `E_LPA` shows no systematic dependence on phase gradient → the correction model has no basis (T1).
- Rigorous-`t_meta` reconstruction is indistinguishable from the idealised model → rigorous Maxwell
  simulation was unnecessary, and the three-tool argument weakens.
- Energy conservation fails at any handoff beyond tolerance → the pipeline is unsound.
- Zemax NSC and SPEOS disagree by > 5% on a matched incoherent case → one model is wrong.

Each of these is measured at a named gate. None can be quietly avoided.

---

## 7. Decision log

Maintained for the life of the programme; seeded from doc 03 §9.

| Date | Decision | Alternatives considered | Rationale | Reversible? |
|---|---|---|---|---|
| 2026-09-30 | Pin to Ansys `v261` (2026 R1) | Track latest | Version coherence across three tools; interop DLLs matched | Yes, at cost |
| 2026-09-30 | RCWA/FDTD unit cell for production; FDTD for validation only | Full-wave throughout | 64 GB ceiling (audit §4) | No |
| 2026-09-30 | N = 5 fan-out, p = 288 nm, Λ = 3.744 µm | N = 4 or 6 | Meets ±20° FOV; super-cell fully rigorous | N adjustable |
| 2026-09-30 | SPEOS for radiometry/perception only | SPEOS for diffraction | SPEOS is incoherent MC ray tracing — physics correctness (risk S4) | No |
| 2026-09-30 | HUD arm primary | Direct-view primary | Exploits installed HOA/HIW plugins | Yes |
| 2026-09-30 | `MCP_VV` as primary OpticStudio driver | `pyzemax-mcp`, `OpticStudioMCPServer` | Only one verified against installed 2026 R1.02; provides provenance journal | Yes |

> Next: `10_References_Bibliography.md` and `07_Paper_Outline_and_Figures.md`.
