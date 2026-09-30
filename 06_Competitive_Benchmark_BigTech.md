# 06 — Competitive Benchmark: Big Tech and Academia

> **Purpose:** Establish honestly where this work sits against the industrial and academic state of
> the art, and identify the defensible gap.
>
> **Stance:** the most valuable thing a consultant does at programme start is **find the paper that
> already did it.** This document contains one such finding (§5), and it changed the programme's
> novelty claim. That correction is worth more than any amount of enthusiasm.

---

## 1. Industrial landscape

### 1.1 The critical distinction: "holographic" is widely misused

Before comparing, a taxonomy — because half of the marketing in this space is not holography.

| Class | Mechanism | Reconstructs a wavefront? | Examples |
|---|---|---|---|
| **True CGH holography** | SLM modulates phase; wavefront reconstructed by diffraction | **Yes** | Samsung SAIT, Stanford, Meta, VividQ, Envisics |
| **Light field / autostereoscopic** | Multiple views via lens array + tracking | **No** | **Sony Spatial Reality Display**, Looking Glass |
| **Waveguide AR (non-holographic)** | Diffractive/reflective pupil expansion of a conventional image | **No** | HoloLens 2, Magic Leap 2, Vision Pro |
| **HOE-based combiners** | Volume hologram used as an *optical element* | No (element is holographic, image is not) | Many HUDs, Digilens |

Our work is in class 1, and uses class-4 elements. **Sony's "Spatial Reality" is class 2 and should
not be presented as a holographic competitor** — a distinction the paper should make explicitly,
because reviewers and readers routinely conflate them.

### 1.2 Company-by-company

| Organisation | Technology | Key numbers | Relation to this work |
|---|---|---|---|
| **Samsung (SAIT)** | Slim-panel holographic video display: LC beam-deflector **steering backlight** + HOE waveguide | **~10 mm thick**; **15° viewing angle** (30× improvement); 150 × 90 mm backlight; 4K @ 30 fps; 140 Gops/s processor | **Closest true-holography product benchmark.** Solves étendue by **time-multiplexed steering (route R2)**. We take route R3. Their 15° validates our ±20° target as ambitious but credible |
| **Stanford (Wetzstein)** | Full-colour 3D holographic AR with **metasurface waveguides**; inverse-designed gratings + AI holography | *Nature* **629**, 791–797 (2024) | **The headline result in the field.** We deliberately do not compete (doc 02 §3.1): different architecture (free-space expander vs. waveguide). ⚠️ **Commonly mis-attributed to Meta — it is Stanford-led** |
| **Meta Reality Labs** | **Waveguide holography** for 3D AR glasses (Jang, Bang, Chae, Lee, Lanman); synthetic-aperture waveguide holography (with Stanford) | *Nat. Commun.* **15**, 66 (2024); *Nat. Photonics* **19**, 854 (2025) | Meta's actual flagship holographic-AR line. Large-étendue mixed reality |
| **POSTECH + Samsung (Rho)** | **Roll-to-plate printable RGB achromatic metalens** for wide-FOV holographic near-eye display | *Nature Materials* **24**, 535–543 (2025) | **Manufacturing-realism benchmark** — proves large-area achromatic metalens production |
| **Sony** | Spatial Reality Display ELF-SR2: 27" 4K LCD + micro-optical lens + gen-2 eye tracking | ±25° H, −40/+20° V; 400 nits; ~100% DCI-P3 | **Light field, not holographic.** Useful as a *commercial viewing-angle and colour-gamut benchmark*, not a technical competitor |
| **Microsoft** | HoloLens 2: SRG waveguide + MEMS laser scanner | ~52° diagonal FOV | Waveguide combiner benchmark; not holographic |
| **Magic Leap** | ML2: dual-layer diffractive waveguide, dynamic dimming | ~70° diagonal | Ambient-contrast benchmark — relevant to our S10 |
| **Apple** | Vision Pro: pancake optics, micro-OLED | — | Not holographic; sets user expectation for image quality |
| **Envisics** | Automotive holographic HUD (true CGH), shipping | — | **Closest commercial analogue to our HUD arm.** Validates commercial relevance |
| **VividQ** | CGH software + waveguide for AR/automotive | — | Algorithmic competitor |
| **Digilens / Lumus / Snap (WaveOptics)** | Waveguide combiners (SRG / reflective) | — | Combiner alternatives |
| **Metalenz** | Commercial metasurface optics (polarisation imaging), shipping in consumer devices | — | **Proves metasurfaces are manufacturable at scale** — supports our fabrication-realism argument |
| **NIL Technology** | Metasurface/DOE nanoimprint manufacturing | — | Manufacturing route for our design rules (S14/S15) |
| **Lumotive** | LCM metasurface beam steering (LiDAR) | — | Proves dynamic metasurface steering commercially |

### 1.3 The key industrial insight

**Samsung chose R2 (steering + tracking); Stanford and Meta chose waveguides; we choose R3 (static
high-NA expansion).** These are genuinely different engineering bets on the same étendue wall
(doc 02 §1.2). Framing the paper around *which bet, and why* is far more interesting than claiming
superiority, and it is defensible: no one of the three has won.

---

## 2. Academic landscape

| Group | Focus | Relevance |
|---|---|---|
| **Gordon Wetzstein (Stanford)** | Neural holography, CGH, metasurface waveguide AR (*Nature* 2024) | **Most directly relevant.** Sets the bar |
| **Felix Heide (Princeton)** | **Neural étendue expanders**, computational optics | **Direct prior art for our concept — see §5** |
| **Federico Capasso (Harvard)** | Metalenses, achromatic metasurfaces, TiO₂ platform | Underpins our material and unit-cell choices |
| **Junsuk Rho (POSTECH)** | Large-area metasurface manufacturing, metaholograms | Fabrication realism |
| **Arka Majumdar (UW)** | Metasurface displays, inverse design | Adjacent |
| **Andrew Maimone (Meta)** | Holographic near-eye displays, étendue expansion | Co-author of the 2020 étendue paper |
| **Byoungho Lee (SNU)** | Holographic displays, HOEs | Adjacent |
| **Mark Brongersma (Stanford)** | Metasurface physics | Underpinning |

---

## 3. Étendue: how everyone compares

The single number that makes the field commensurable (doc 02 §1).

| Approach | Route | Expansion | Trade-off |
|---|---|---|---|
| Bare SLM (3.74 µm) | — | ×1 | ±4°, unusable |
| Samsung steering BLU | R2 | ~effective 30× angle gain | Needs tracking + LC deflector; sequential |
| Kuo et al. 2020 (optimised mask) | R3 | ~×10–20 | Speckle; algorithmic cost |
| **Tseng et al. 2024 (neural expander)** | **R3** | **×64** | **Idealised thin-element model; realisability unaddressed** |
| **Wang et al. 2026 (binary π-metasurface)** | **R3** | reported in ref. | **Binary two-level phase; rigorous angular/spectral response unaddressed** |
| Stanford 2024 waveguide | waveguide + pupil replication | — | Fabrication complexity |
| Meta 2024/2025 waveguide holography | waveguide, synthetic aperture | large | Fabrication complexity |
| **This work (Meta-EE)** | **R3** | **×25 (target)** | **Rigorously modelled; realisability and LPA error quantified** |

> **Note our ×25 is deliberately *less* than Tseng's ×64.** That is not a shortfall to apologise for —
> it is the point. Their 64× is achieved with an idealised mask; our 25× is a *physically realisable,
> rigorously simulated* structure with a quantified error budget. **Comparing the two is the paper's
> most interesting figure**, and the honest framing is "what does idealisation cost?" rather than
> "ours is bigger."

---

## 4. Simulation methodology gap

This is where the field is genuinely thin, and where a three-tool pipeline earns its place.

| Capability | Samsung | Stanford 2024 | Kuo 2020 | Tseng 2024 | Metalens papers | **This work** |
|---|---|---|---|---|---|---|
| Rigorous Maxwell (FDTD/RCWA) of the element | Partial | **Yes** (gratings) | No | No | **Yes** | **Yes** |
| **Full-wave finite-patch reference (LPA error)** | No | No | No | No | **Rarely** | **Yes — core** |
| System-level ray/physical optics | Yes | Partial | No | No | Rarely | **Yes** |
| Ghost / stray-light attribution | Partial | No | No | No | No | **Yes (`.lpf`)** |
| **Photometry, ambient contrast, colour gamut** | Partial | No | No | No | No | **Yes** |
| **Human-vision perceptual validation** | No | No | No | No | No | **Yes** |
| **Quantified cross-tool error budget** | No | No | No | No | No | **Yes** |
| Formal DOE / Monte-Carlo tolerancing | Partial | No | No | No | Rarely | **Yes** |
| Experimental prototype | **Yes** | **Yes** | **Yes** | **Yes** | Often | **No** |

Two honest readings of this table:

- **The bottom row is our weakness.** Everyone else built hardware. We must not pretend otherwise
  (doc 02 §4.4), and must compensate with rigour and reproducibility.
- **The middle rows are our strength, and they are genuinely empty for everyone else.** Nobody
  carries a metasurface display design from Maxwell's equations through to *what a human sees in
  sunlight*, with a measured error budget at every handoff.

---

## 5. The prior-art correction **(read this before writing anything)**

### What we found

Two hits, the second decisive.

**(a) Tseng, Kuo, Baek, … Heide — "Neural étendue expander for ultra-wide-angle high-fidelity
holographic display", *Nature Communications* 15 (2024), DOI 10.1038/s41467-024-46915-3**
(arXiv:2109.08123) demonstrates **learned, deterministic, non-random étendue expanders achieving 64×
expansion** for full-colour, retinal-resolution holographic display. Their supplementary material
explicitly benchmarks learned expanders against **random binary expanders** and shows the learned
design winning.

**(b) ⭐ Wang, Zhou, Tseng, Chu, Chen, Froech, Majumdar & Heide — "Holographic display étendue
expansion with a binary π-metasurface", *Optics Letters* (2026), DOI 10.1364/OL.613712.**
This is, precisely and by name, **a metasurface étendue expander for holographic display** — from the
Princeton (Heide), UW (Majumdar) and Harvard-lineage (W.-T. Chen) groups.

Together with **Kuo, Waller, Ng & Maimone, SIGGRAPH 2020**, this establishes optimised static
scattering masks — *including metasurface implementations* — as mature prior art.

### What it invalidates

The original framing — *"a deterministic (non-random) metasurface étendue expander"* as a headline
novelty — **is comprehensively not novel.** Wang et al. (2026) is the same device concept.

Had this surfaced at month 12 rather than month 0, a substantial part of the programme would have
been wasted. It has been corrected in `02` §4.3 and `03`. **This is the single highest-value hour
spent in the whole planning exercise.**

### What survives, and is stronger

Both Kuo and Tseng model the expander as an **idealised thin-element phase mask**: scalar,
infinitely thin, angle-independent, polarisation-independent, dispersion-free. Wang et al. use a
deliberately simple **binary π** structure. None of them asks:

- Can such a phase pattern be **physically realised** in a fabricable nanostructure with *continuous*
  phase control, and what does the realisation cost?
- What does the **true angular and spectral response** of real nanopillars do to performance across
  a ±20° field and three wavelengths?
- Does the **local-periodicity approximation** — implicit in *any* metasurface realisation — even
  hold at the steep phase gradients étendue expansion requires? **Ansys's own metalens documentation
  states LPA "is most likely to break down if neighbouring meta-atoms are vastly dissimilar… if the
  phase response changes abruptly"** — which is exactly a fan-out super-cell. The tool vendor flags
  the problem; no one quantifies it.
- What does the resulting display look like to a **human observer in real ambient light**?

**These four questions are unclaimed, and all four require exactly the tri-tool pipeline we have.**

Wang et al. (2026) actually *helps* us: it provides a concrete, published, binary-π device to
benchmark the methodology against, and its very simplicity (binary, two-level phase) makes the
realisability question sharper rather than duller.

### Revised one-line positioning

> *Prior work establishes **what pattern** an étendue expander should have. This work establishes
> **what happens when you have to build one** — and what the user finally sees.*

This is a better paper than the original framing, and it is defensible.

---

## 6. Differentiation summary

| Dimension | Field's position | Ours |
|---|---|---|
| Étendue route | R2 (Samsung) or R3-idealised (Kuo, Tseng, Wang) or waveguide (Stanford, Meta) | **R3, physically rigorous** |
| Element model | Idealised thin phase mask | **Rigorous vector Maxwell, angle/λ/pol resolved** |
| LPA | Assumed valid | **Measured, mapped, corrected** |
| Validation endpoint | PSNR on a sensor | **Luminance, ambient contrast, gamut, human vision** |
| Error budget | Absent | **Measured at 7 handoffs** |
| Tolerancing | Ad hoc | **Formal DOE + Monte-Carlo yield** |
| Reproducibility | Partial | **Full driver/config/data release** |
| Hardware | **Prototypes** | **None — our weakness, stated plainly** |

---

## 7. Competitive risks

| Risk | Response |
|---|---|
| Heide/Princeton publish a *rigorously realisable* neural expander first | Most likely scoop route (risk S1). Move fast on M1; our perception-layer validation is still unduplicated |
| Reviewers read this as "Tseng 2024 with extra steps" | **Pre-empt in the abstract.** Lead with the realisability gap and the LPA measurement, never with "étendue expander" |
| Stanford or Meta extend their waveguide work to free-space expanders | Different architecture; emphasise the methodology contribution, which is architecture-independent |
| "Simulation-only" dismissal | Risk S2: rigorous cross-validation, manufacturable design rules, honest scope statement |

---

## 8. Suggested venues

| Venue | Fit | Notes |
|---|---|---|
| **Optics Express** | **High** | Strong on rigorous simulation methodology; open access; receptive to simulation-only work |
| **Light: Science & Applications** | Medium-high | If the realisability gap result is strong |
| *Nature Communications* | Medium | Realistic only with the stretch goals delivered *and* a strong LPA result |
| **Optica** | Medium-high | If framed tightly around the LPA/realisability measurement |
| *Applied Optics* | High (fallback) | Solid, appropriate for a systems/methods paper |
| *Journal of Optical Microsystems / OSA Continuum* | Fallback | |
| *Optics Express* (software) / *SoftwareX* | — | **Secondary paper** on the MCP orchestration layer (doc 08 §5) |

**Recommendation: target *Optics Express*.** It is the natural home for rigorous, reproducible,
simulation-led optical engineering, and the realisability + LPA + perception combination is a
comfortable fit. Reserve *Light: Sci. Appl.* / *Optica* as an upgrade if M1 produces a striking LPA
result.

> References with full citations: `10_References_Bibliography.md`.
