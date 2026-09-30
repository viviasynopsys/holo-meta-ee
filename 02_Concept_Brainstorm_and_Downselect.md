# 02 — Concept Brainstorm and Down-Select

> **Purpose:** Generate the realistic solution space for a metasurface/DOE holographic display,
> then choose one with a defensible, written rationale. Written from the standpoint of an optical
> systems architect who has to sign off on a programme, not a student picking a topic.
>
> **Prerequisite:** `01_Capability_Audit_Installed_Stack.md` (hardware and toolchain limits).

---

## 1. First, the physics that decides everything

Before generating concepts, we fix the number that kills most holographic display ideas. Every
concept below is judged against it.

### 1.1 The étendue / space-bandwidth wall

A phase-only SLM of pixel pitch `p` can steer light only within

```
sin θ_max = λ / (2p)
```

Take a realistic high-end phase LCoS panel — **3840 × 2160 at p = 3.74 µm**, active area
**14.362 mm × 8.078 mm = 116.0 mm²** — at λ = 532 nm:

```
sin θ_max = 532e-9 / (2 × 3.74e-6) = 0.07112   ->   θ_max = ±4.078°
```

Étendue available from the panel:

```
Ω_SLM = 2π (1 − cos 4.078°) = 0.015913 sr
E_SLM = 116.0 mm² × 0.015913 sr ≈ 1.846 mm²·sr
```

Étendue *demanded* by a usable display — a 10 mm eyebox with a ±20° field:

```
A_eyebox = π × (5 mm)² = 78.54 mm²
Ω_FOV    = 2π (1 − cos 20°) = 0.37891 sr
E_req    = 78.54 × 0.37891 ≈ 29.76 mm²·sr
```

**Deficit = 29.76 / 1.846 ≈ 16.1×.**

> **This single number is the thesis of the whole project.** Étendue is conserved by any lossless
> *non-scattering* optic — you cannot magnify your way out of it. A holographic display is therefore
> not primarily an *aberration* problem (Zemax's classical domain) nor a *nanostructure* problem
> (Lumerical's domain) but an **information-throughput** problem that spans both. Any concept that
> does not explicitly state how it closes a ~16× étendue gap is not a serious concept.

### 1.2 The three (and only three) ways to close the gap

| Route | Mechanism | Cost |
|---|---|---|
| **R1 — Smaller pixels** | Reduce `p` toward λ/2 | Not available: no such SLM exists at scale. Dead end for us |
| **R2 — Time multiplexing** | Steer a small eyebox to the pupil, eye-tracked | Needs tracking + steering; multiplies frame rate; *does not* increase instantaneous étendue |
| **R3 — Angular/spatial multiplexing by a static high-NA element** | A metasurface/DOE scatters each SLM order into many controlled directions, filling the étendue; the CGH pre-compensates | Needs the element's transfer function known **exactly**; speckle and crosstalk; **this is where metasurfaces win** |

R3 is the metasurface's natural home, and it is the only route where rigorous Maxwell simulation is
genuinely *required* rather than decorative. **R3 is therefore the spine of the recommended concept**,
with R2 available as a multiplier.

### 1.3 Why this makes a three-tool paper natural

R3 forces a genuine multi-scale chain, and each tool owns a scale that the others physically cannot:

- The element is **sub-wavelength** → only rigorous Maxwell (Lumerical) gives its true complex,
  angle- and polarisation-resolved response.
- The system is **centimetre-to-metre** with real aberrations, ghosts and tolerances → OpticStudio.
- The deliverable is **a perceived image in ambient light** → SPEOS (photometry, colour, stray light,
  human vision), which is the part almost every metasurface-display paper omits.

---

## 2. Candidate concepts

Twelve candidates, generated to span the space rather than to flatter one answer.

| # | Concept | One-line description | Étendue route |
|---|---|---|---|
| **C1** | Metalens-array light-field hybrid | MLA over a panel; holographic sub-images per lenslet | R3 (weak) |
| **C2** | **Metasurface étendue expander (Meta-EE)** | Static engineered metasurface after the SLM multiplies the angular spectrum N×N; CGH pre-compensates | **R3** |
| **C3** | Metasurface waveguide combiner (AR) | In/out-coupling metasurface gratings in a TIR slab for holographic AR | R3 + pupil replication |
| **C4** | Metasurface steering backlight | Slim-panel holographic display with a directional backlight + HOE | R2 |
| **C5** | Polarisation-multiplexed multi-plane metasurface | Jones-matrix metasurface gives independent holograms per polarisation → multi-depth | R3 (partial) |
| **C6** | Dispersion-engineered achromatic metalens | Single metalens corrected across RGB for full-colour CGH | Enabler, not expander |
| **C7** | Transparent diffractive projection screen | DOE/HOE screen with engineered angular scatter; projector illuminated | R3 (incoherent) |
| **C8** | Metasurface holographic HUD + windshield | Virtual-image HUD; metasurface picture-generation unit; windshield as combiner | R3 + R2 |
| **C9** | Metasurface exit-pupil expander (EPE) | Pupil replication by cascaded metasurface gratings | R3 |
| **C10** | Co-designed "computational" metasurface | Inverse-design the metasurface *and* the CGH jointly, end-to-end differentiable | R3 |
| **C11** | Eye-tracked steered eyebox | Angular-multiplexed metasurface + tracking steers a small high-quality eyebox | R2 |
| **C12** | Metasurface + volume-HOE hybrid | Metasurface for high-angle work, Kogelnik HOE for wavelength selectivity | R3 + R2 |

---

## 3. Scoring

Weighted 1–5 (5 best). Weights reflect what actually determines whether a paper lands and a programme
finishes.

| Criterion | Weight | Rationale |
|---|---|---|
| **Novelty / publishability** | 20% | Must not merely replay a published result |
| **Exercises all three tools** | 20% | The stated objective — each tool must be *necessary* |
| **Feasible on 64 GB / 8 GB GPU** | 20% | Ruthlessly enforced; see audit §4 |
| **Differentiation vs. big tech** | 15% | Must not be a worse version of Meta's Nature paper |
| **Quantitative, falsifiable results** | 15% | Referees want numbers with error bars |
| **Fabrication realism** | 10% | Must be manufacturable in principle |

| # | Novelty (20) | 3-tool (20) | Feasible (20) | Differentiation (15) | Quantitative (15) | Fab (10) | **Total** |
|---|---|---|---|---|---|---|---|
| C1 | 2 | 3 | 4 | 2 | 3 | 4 | **2.90** |
| **C2** | **4** | **5** | **4** | **4** | **5** | **4** | **4.35** |
| C3 | 3 | 4 | 3 | 2 | 4 | 3 | **3.25** |
| C4 | 2 | 3 | 4 | 2 | 3 | 3 | **2.85** |
| C5 | 4 | 4 | 4 | 4 | 4 | 3 | **3.90** |
| C6 | 2 | 4 | 4 | 2 | 4 | 3 | **3.30** |
| C7 | 3 | 4 | 5 | 4 | 3 | 5 | **3.95** |
| **C8** | **4** | **5** | **4** | **5** | **4** | **4** | **4.40** |
| C9 | 3 | 4 | 3 | 2 | 4 | 3 | **3.25** |
| **C10** | **5** | **5** | **3** | **5** | **4** | **3** | **4.25** |
| C11 | 3 | 3 | 4 | 3 | 4 | 4 | **3.45** |
| C12 | 3 | 5 | 4 | 3 | 4 | 3 | **3.80** |

**Leaders: C8 (4.40), C2 (4.35), C10 (4.25), C7 (3.95), C5 (3.90).**

### 3.1 Why the leaders lead, and why the others fail

- **C3 / C9 (waveguide combiner, EPE) scored low on differentiation deliberately.** Meta Reality Labs
  published full-colour 3-D holographic AR with *metasurface waveguides* in Nature (2024). Entering
  that exact lane with a laptop and no fab is competing on their terms and losing. We should
  **cite it as the benchmark, not re-run it.**
- **C4 (steering backlight)** is essentially Samsung SAIT's slim-panel architecture. Same problem.
- **C1, C6** are enablers or incremental; neither closes the étendue gap, so neither carries a paper.
- **C7** is genuinely feasible and fabricable and plays beautifully to SPEOS — but it is largely an
  *incoherent* screen-scatter problem, which under-uses Lumerical's rigour. Excellent **fallback**.
- **C10** is the most novel and the most likely to overrun. Differentiable end-to-end co-design of
  nanostructure *and* CGH is a big claim requiring a robust adjoint pipeline. Best treated as a
  **stretch objective layered on top of a solid base**, not as the base itself.

---

## 4. Recommendation

> ### Adopt a fused concept: **C2 + C8**, with **C10** as a stretch goal and **C5** as an extension.
>
> **Working title — "Meta-EE": an inverse-designed, dispersion-engineered metasurface étendue
> expander for a full-colour holographic virtual-image display, validated end-to-end from Maxwell's
> equations to human perception.**

### 4.1 The system in one paragraph

RGB lasers illuminate a phase-only LCoS SLM. Immediately after it sits a **static metasurface étendue
expander** — a sub-wavelength, polarisation-insensitive, RGB-corrected element that deterministically
fans each incident order into an N×N array of high-angle beamlets, multiplying available étendue by
≈16×. Because the element's complex transfer function is *known rigorously* from RCWA/FDTD (not
assumed, and not random as in a diffuser), the CGH engine can **pre-compensate** for it, recovering a
sharp reconstruction across the enlarged eyebox. A relay and combiner (a windshield in the HUD
configuration, exploiting the installed SPEOS HIW/HOA plugins) forms the virtual image. Performance is
then evaluated not only in optical metrics but in **perceptual** ones — ambient-light contrast, colour
gamut, eyebox luminance uniformity — under real illumination.

### 4.2 Why this is the right call

1. **It is the only candidate where all three tools are load-bearing.** Remove Lumerical and the
   transfer function is guesswork, so the CGH pre-compensation cannot work. Remove Zemax and there is
   no eyebox, ghost-order or tolerance analysis. Remove SPEOS and there is no statement about what a
   human actually sees in sunlight. Each tool is *necessary*, which is exactly the showcase the
   objective asks for.
2. **It converts our hardware ceiling into the contribution.** The pre-compensation is only as good as
   the local-periodicity approximation underlying the phase library. Our 30 µm FDTD budget is exactly
   right for *quantifying the LPA error versus deflection angle* — a number the field asserts but
   rarely measures (audit §4.2).
3. **It differentiates cleanly from big tech.** Meta owns metasurface *waveguides*; Samsung owns
   *steering backlights*; Sony's Spatial Reality Display is light-field, not holographic. **Nobody
   owns "rigorously characterised deterministic metasurface étendue expansion with
   perception-level validation."** That is a defensible lane.
4. **It degrades gracefully.** If the inverse design underperforms, a parametric pillar library still
   yields a complete paper. If the HUD arm stalls, the direct-view arm (C7) still delivers. There is
   no single point of programme failure.
5. **It is honest about SPEOS.** SPEOS is incoherent Monte-Carlo ray tracing and **cannot reconstruct
   a hologram**. We use it where it is rigorous — radiometry, colour, stray light, human vision — and
   feed it via BSDF (`bsdf_creation.proto`) and via sources derived from the coherent result. Any plan
   that asks SPEOS to diffract would be rejected by a competent referee; ours does not.

### 4.3 Explicit novelty claims — *revised after prior-art check*

> **Prior-art correction (important).** An initial framing of this concept claimed "a deterministic,
> non-random métasurface étendue expander" as the headline novelty. **Two literature checks
> invalidated that claim outright:**
>
> 1. Kuo, Waller, Ng & Maimone (SIGGRAPH 2020) established optimised static scattering masks, and
>    Tseng, Kuo, Baek, … Heide (*Nature Communications*, 2024) demonstrated **learned "neural étendue
>    expanders" achieving 64× expansion** at full colour and retinal resolution.
> 2. **Decisively:** Wang, Zhou, Tseng, Chu, Chen, Froech, Majumdar & Heide,
>    **"Holographic display étendue expansion with a binary π-metasurface", *Optics Letters* (2026),
>    DOI 10.1364/OL.613712** — this is *literally a metasurface étendue expander for holographic
>    display*, from the Princeton/UW/Harvard groups.
>
> **The device concept is therefore prior art, not our contribution.** Finding this in month 0 cost
> a morning; finding it in month 12 would have cost the programme. It is recorded here rather than
> quietly deleted. See `06_Competitive_Benchmark_BigTech.md` §5.

**The gap all of this prior work leaves open.** Kuo, Tseng and Wang et al. design and evaluate their
expanders using **idealised thin-element models** — scalar, infinitely thin, angle-independent,
polarisation-independent, dispersion-free — or, in Wang's case, a deliberately simple *binary* π
structure. None of them asks:

- What does the **true vector-Maxwell response** of a realisable nanostructure — angle-, wavelength-
  and polarisation-resolved — do to expander performance across a ±20° field and three wavelengths?
- Does the **local-periodicity approximation**, implicit in *any* metasurface realisation, even hold
  at the steep, abruptly-varying phase gradients étendue expansion demands? **Ansys's own
  documentation warns this is precisely where LPA "is most likely to break down"** — the vendor flags
  it, and nobody measures it.
- What does the resulting display look like to a **human observer in real ambient light** —
  luminance, ambient contrast, colour gamut?

**These questions are unclaimed, and all three require exactly the tri-tool pipeline we have.**

### Revised novelty claims, in order of strength

1. **The physical-realisability gap, quantified.** How much performance is lost when an idealised
   étendue-expander phase mask is replaced by a *rigorously simulated, fabricable* metasurface with
   true angular, spectral and polarisation response? Unmeasured. This is the central result.
2. **A quantified validity map for the local-periodicity approximation** at the steep phase gradients
   étendue expansion requires, with full-wave FDTD as reference and a correction model — targeting
   the exact regime the tool vendor identifies as most suspect.
3. **The first end-to-end, cross-validated Maxwell-to-perception methodology** for a holographic
   display: a measured error budget at all seven tool handoffs, closing in photometric and perceptual
   units that no metasurface-display paper reports.
4. *(stretch, C10)* Joint adjoint optimisation of nanostructure and CGH — the physically-rigorous
   analogue of the neural expander's idealised optimisation.
5. *(extension, C5)* Polarisation multiplexing for multi-depth reconstruction. **Note constraint K5**
   (doc 03 §9): Zemax ray tracing supports only polarisation-*insensitive* meta-atoms, so this
   extension must run through full-wave plus our own propagation.

**Why this framing is stronger than the original.** It positions the work as the *rigorous physical
counterpart* to an established computational result, rather than competing with it. Kuo, Tseng and
Wang answer *"what pattern should the expander have?"*; we answer *"what happens when you actually
have to build it, and what does the user finally see?"* Those are complementary, and the second is
unclaimed. Crucially, Wang et al. (2026) now gives us a **concrete, published device to benchmark the
methodology against** — which makes the methodology paper easier to write, not harder.

Claims 1–3 make this a **methods** paper as well as a device paper — and methods papers with a
reproducible pipeline age far better than single-device demonstrations.

### 4.4 What we are explicitly NOT doing

Scope discipline, stated up front so it can be defended later:

- **Not fabricating.** This is a simulation and methodology paper. Designs will be constrained to
  manufacturable geometries (aspect ratio ≤ 10:1, minimum feature ≥ 60 nm, single-layer
  DUV/NIL-compatible) and we will say so, but we will not claim measured results.
- **Not building a waveguide combiner.** Meta's lane; cited, not contested.
- **Not claiming real-time CGH.** Computation time will be reported honestly.
- **Not using SPEOS for diffraction.** See §4.2.5.

### 4.5 Fallback ladder

| If this fails… | Fall back to | Paper still viable? |
|---|---|---|
| Inverse design (C10) does not converge | Parametric pillar library sweep | **Yes** — full paper |
| RGB achromatisation insufficient | Field-sequential single-λ per frame | **Yes** — reduced claim |
| Zemax dynamic-link co-sim too slow | Pre-tabulated RCWA efficiency vs. angle/λ | **Yes** — add interpolation error to budget |
| HUD/windshield arm stalls | Direct-view transparent screen (C7) | **Yes** — SPEOS role preserved |
| Étendue expansion under-delivers | Report as characterisation + LPA study | **Yes** — methods paper survives |

The ladder matters: **every rung still yields a publishable paper.** That is the test of a
well-posed programme.

---

## 5. Down-selected architecture summary

```
RGB lasers (457 / 532 / 638 nm)
        |
   beam shaping / collimation
        |
   Phase-only LCoS SLM  (3.74 µm, 3840x2160, 14.362 x 8.078 mm)    E = 1.846 mm2·sr
        |
   [ META-EE ]  static metasurface etendue expander               x 25
        |        - sub-wavelength TiO2 / SiN pillars on glass
        |        - deterministic 5x5 angular fan-out
        |        - RGB dispersion-engineered, pol-insensitive
        |        - transfer function from Lumerical RCWA (+FDTD-validated)
        |
   relay / Fourier filter (order & DC management)
        |
   combiner: windshield (HUD arm)  OR  transparent screen (direct-view arm)
        |
   EYEBOX  ~10 mm,  FOV ~ +-20.4deg                               E = 29.76 mm2·sr
        |
   human observer  -> SPEOS Human Vision, ambient contrast, colour
```

Full technical definition follows in `03_System_Architecture_HoloMeta.md`;
the tool-by-tool dataflow and validation gates in `04_Simulation_Workflow_Tri_Tool.md`.
