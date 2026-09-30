# 00 — Executive Summary

> **Brief:** Use Ansys Lumerical, Zemax OpticStudio and SPEOS (all installed, all LLM-drivable via
> MCP) to their fullest, on a holographic display built from metasurfaces or DOEs, and produce a
> research article that showcases the toolchain — benchmarked against Sony, Samsung and the other
> big-tech players.
>
> **This document is the answer in two pages.** Everything else is detail.

---

## 1. Recommendation

> Build and publish **"Meta-EE"** — a full-colour holographic virtual-image display using a
> **metasurface étendue expander**, simulated end-to-end **from Maxwell's equations to human
> perception**, with a **measured error budget at every tool handoff**.
>
> **The paper's contribution is not the device. It is the rigour.**

**Target:** *Optics Express*, 18 months, ~9 person-months, on the workstation you already have.

---

## 2. The problem worth solving

A holographic display fails on one number. A 3840×2160 phase SLM at 3.74 µm pitch can steer light
only ±4.078° at 532 nm, giving an étendue of **1.846 mm²·sr**. A usable display — 10 mm eyebox, ±20°
field — needs **29.76 mm²·sr**.

> **A ~16× étendue deficit. Étendue is conserved; you cannot magnify your way out of it.**

This single number is why holographic displays are not on your desk, and it is the axis along which
every industrial player can be compared. There are only three escapes: smaller pixels (no such panel
exists), time-multiplexed steering (Samsung's bet), or **static high-NA angular multiplexing —
which is where metasurfaces win, and the only route where rigorous Maxwell simulation is genuinely
required rather than decorative.**

---

## 3. What the audit found

All three products are installed under **one version-coherent Ansys 2026 R1 (`v261`) tree**. The
decisive discovery: **the official interop bridges are already on disk**, including a live one.

| Asset (verified on disk) | Why it matters |
|---|---|
| `lumerical-metalens-2026R1-1.dll` | Metasurface → Zemax sequential surface |
| **`lumerical-sub-wavelength-dynamic-link-2026R1-1.dll`** | **Live Lumerical RCWA called during the Zemax ray trace** |
| `srg_*_RCWA` family, `hologram_kogelnik.dll` | Rigorous gratings; volume-HOE baseline |
| `rcwa-engine.exe`, `lumopt`/`lumopt2` | Fast periodic solving; **adjoint inverse design** |
| SPEOS `HOA_*`/`HIW_*` plugins | **HUD optical analysis, windshield, wedge angle, virtual image** |
| SPEOS gRPC grammar incl. `bsdf_creation.proto`, `lpf/` | Scriptable SPEOS; ray-level stray-light forensics |
| `MCP_VV` (39 tools, 102 tests, verified vs. OpticStudio 2026 R1.02) | LLM-drivable Zemax **with an audit journal** |
| `pylumerical-mcp` (26 guideline topics) | LLM-drivable Lumerical |

**The hard interop problem is already solved.** That is a far stronger starting position than the
field's norm.

### The constraint that shapes everything

**64 GB of RAM caps rigorous full-wave FDTD at a ~30 µm patch.** A display-sized metasurface would
need petabytes. Everyone in the field — Meta, Samsung, everyone — therefore uses the **local
periodicity approximation (LPA)**.

**But the LPA's error is universally asserted and almost never measured.** Ansys's own documentation
warns it "is most likely to break down if neighbouring meta-atoms are vastly dissimilar… if the phase
response changes abruptly" — which describes an étendue expander exactly.

> **A 30 µm patch is precisely large enough to compute the rigorous reference the approximation
> should be checked against. The hardware ceiling becomes the paper's central measurement.**

---

## 4. Two prior-art corrections that reshaped the plan

This is the most valuable output of the planning exercise.

**An initial framing claimed "a deterministic metasurface étendue expander" as the novelty. Two
literature checks destroyed it:**

1. Tseng, Kuo, Baek & Heide, *Nature Communications* **15** (2024) — **neural étendue expanders, 64×**.
2. ⭐ **Wang, Zhou, Tseng, Chu, Chen, Froech, Majumdar & Heide, "Holographic display étendue expansion
   with a binary π-metasurface", *Optics Letters* (2026)** — *literally the same device concept.*

**Cost of finding this in month 0: one morning. Cost of finding it in month 12: the programme.**

### What survives — and is stronger

All of that prior work models the expander as an **idealised thin-element phase mask**: scalar,
infinitely thin, angle-independent, polarisation-independent, dispersion-free. None of it asks what
happens when you have to *build* one.

| # | Claim | Status |
|---|---|---|
| **1** | **The physical-realisability gap, quantified** — what does idealisation actually cost? | **Unclaimed** |
| **2** | **LPA validity mapped and corrected** at steep gradients | **Unclaimed** (vendor-flagged, unmeasured) |
| **3** | **First Maxwell→perception pipeline** with a measured 7-handoff error budget | **Unclaimed** |

> **Revised positioning:** *Prior work establishes **what pattern** an étendue expander should have.
> This work establishes **what happens when you have to build one** — and what the user finally sees.*

Wang et al. (2026) now *helps*: it gives us a published device to benchmark the methodology against.

---

## 5. Why this uses all three tools, necessarily

Each owns a scale the others physically cannot reach. **Remove any one and a claim collapses.**

| Scale | Physics | Tool | Remove it and… |
|---|---|---|---|
| 10 nm – 4 µm | Vector Maxwell | **Lumerical** | the transfer function is guesswork; CGH pre-compensation fails |
| 4 µm – 15 mm | Coherent diffraction | **Python ASM** + Zemax POP | no reconstruction |
| 1 mm – 1 m | Geometrical + physical optics | **Zemax** | no eyebox, ghosts or tolerances |
| 0.1 m – observer | Photometry, colour, perception | **SPEOS** | no statement of what a human sees |

**SPEOS's role is deliberately bounded.** It is incoherent Monte-Carlo ray tracing and **cannot
reconstruct a hologram** — so we never ask it to. It receives per-order *energy* via LSWM and answers
only incoherent questions: ambient contrast, colour gamut, eyebox uniformity, stray light, human
vision. Those rows appear in essentially **no** metasurface-display paper, and they are much of the
novelty.

---

## 6. The design, in one block

```
RGB lasers -> phase LCoS SLM (3.74 um, 3840x2160)        E = 1.846 mm2·sr, +-4.078 deg
        |
   [ META-EE ]  13x13 TiO2 pillars, p = 288 nm, h = 600 nm
        |       super-cell 3.744 um -> 5x5 equal-energy fan-out          x25
        |
   relay + order stop -> windshield combiner (SPEOS HOA/HIW)
        |
   EYEBOX 10 mm, +-20.4 deg                              E = 29.76 mm2·sr
        |
   observer -> SPEOS Human Vision
```

Every parameter is *derived*, not chosen (doc 03 §3). The pivotal consequence:

> **The 13×13 super-cell is small enough to solve rigorously in full, and a 30 µm patch of 8×8
> super-cells is exactly the largest full-wave reference 64 GB permits.** The design was built to fit
> the measurement.

**The photometric budget closes with ~4× margin** — ≈61 000 cd/m² against a 15 000 cd/m²
sunlight-readable requirement. A sunlight-readable holographic HUD is feasible on these numbers,
which is not obvious and is worth stating.

---

## 7. Benchmark against big tech

| Player | Bet | Result | Our relation |
|---|---|---|---|
| **Samsung SAIT** | Steering backlight (time-multiplex) | 10 mm panel, **15° viewing**, 4K@30 | Industrial baseline; different route |
| **Stanford** (Wetzstein) | Metasurface waveguide | *Nature* 629, 791 (2024) | Field-defining; **we do not compete** |
| **Meta** (Jang, Lanman) | Waveguide holography | *Nat. Commun.* 15, 66 (2024) | Adjacent |
| **POSTECH + Samsung** (Rho) | RGB achromatic metalens, roll-to-plate | *Nat. Mater.* 24, 535 (2025) | Manufacturing realism |
| **Princeton/UW** (Heide, Majumdar) | **Metasurface étendue expander** | *Opt. Lett.* (2026) | **Our device prior art** |
| **Sony** | Light field + eye tracking | ELF-SR2, ±25° | ⚠️ **Not holographic** — a widely repeated confusion |

**The methodology table is where we are alone:** nobody carries a metasurface display from Maxwell to
*what a human sees in sunlight* with a measured error budget. Our weakness is equally clear and
stated plainly: **everyone else built hardware; we do not.**

---

## 8. Plan

| Milestone | Month | Evidence |
|---|---|---|
| **M0** Toolchain proven | 1 | End-to-end trivial case, full provenance; **licence entitlements confirmed** |
| **M1** LPA quantified | 4 | Error map + correction model — **claim 2 secured** |
| **M2** Reconstruction demonstrated | 7 | ≥25 dB PSNR through the rigorous model — **claim 1 secured** |
| **M3** Maxwell→perception closed | 11 | Luminance, contrast, gamut — **claim 3 secured** |
| **M4** Tolerance-bounded | 14 | Yield >80%, error budget complete |
| **M5** Submitted | 18 | Manuscript + reproducibility package |

**~120–200 hours of compute over 18 months** — ample margin on your hardware.

Two properties make this plan robust:

1. **The riskiest work is scheduled first.** The three severity-12 risks are all de-risked in month 1.
2. **Every exit point still yields a paper.** M1 alone supports a solid methods paper. There is no
   single point of programme failure.

---

## 9. What must be built

| Item | Status |
|---|---|
| Rigorous solving, inverse design, interop, HUD analysis, DOE | **All present** |
| **CGH engine** (PyTorch ASM + SGD) | Build — core original code |
| **LPA-error harness** | Build — the scientific core |
| **Cross-tool validation/provenance layer** | Build — required for the reproducibility claim |
| **SPEOS MCP server** | Optional, high value — gRPC grammar + an existing retargeting meta-prompt make it ~3–5 weeks |

Completing the SPEOS server would give a **fully LLM-orchestrated tri-tool optical pipeline** — a
legitimate secondary publication (doc 08 §5).

---

## 10. The five things that matter most

1. **The étendue deficit is ~16×.** Every design decision follows from it.
2. **The device is prior art; the rigour is not.** Lead with the realisability gap, never with
   "étendue expander" (doc 07 §5).
3. **64 GB caps FDTD at 30 µm — and that is exactly the LPA reference size.** Constraint became
   contribution.
4. **SPEOS must never be asked to diffract.** Highest-severity scientific risk (15/25), fully
   answerable, and answered by design.
5. **Zemax POP is invalid beyond ~20° half-angle — our design sits at ±20.4°.** Our own ASM is the
   primary propagator; POP is demoted to a low-angle cross-check. Found in the docs, in month 0.

---

## Document map

| Doc | Contents |
|---|---|
| [01 — Capability Audit](01_Capability_Audit_Installed_Stack.md) | What is installed, verified on disk; hardware limits |
| [02 — Concept Brainstorm](02_Concept_Brainstorm_and_Downselect.md) | 12 concepts scored; étendue physics; prior-art correction |
| [03 — System Architecture](03_System_Architecture_HoloMeta.md) | Specs, derivation, budgets, hard tool constraints |
| [04 — Simulation Workflow](04_Simulation_Workflow_Tri_Tool.md) | 11 stages, gates G0–G11, code, costs |
| [05 — Work Plan](05_Work_Plan_Milestones.md) | Phases, milestones, schedule, go/no-go |
| [06 — Competitive Benchmark](06_Competitive_Benchmark_BigTech.md) | Industry and academia; the prior-art analysis |
| [07 — Paper Outline](07_Paper_Outline_and_Figures.md) | Structure, 12 figures, pre-answered objections |
| [08 — Automation / MCP](08_Automation_MCP_Orchestration.md) | Orchestration, the SPEOS server gap |
| [09 — Risk Register](09_Risk_Register_and_Validation.md) | 25 risks, 5-layer validation, falsifiability |
| [10 — References](10_References_Bibliography.md) | 38 citations + Ansys example inventory + attribution fixes |
