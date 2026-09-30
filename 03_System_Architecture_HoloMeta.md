# 03 — System Architecture: "Meta-EE" Holographic Display

> **Purpose:** Turn the down-selected concept into a specification concrete enough that every
> simulation in `04` has defined inputs, defined outputs and a defined acceptance threshold.
> Numbers here are *design targets*, chosen to be self-consistent and simulatable on the audited
> hardware. They are the contract the rest of the programme is measured against.

---

## 1. Top-level specification

| # | Parameter | Target | Threshold (min. acceptable) | Verified by |
|---|---|---|---|---|
| S1 | Wavelengths | 457 / 532 / 638 nm | ±5 nm | Lumerical |
| S2 | Eyebox | 10 mm diameter | 8 mm | Zemax + SPEOS |
| S3 | Field of view | ±20° (40° full) | ±15° | Zemax |
| S4 | Étendue expansion | **×25** | ×12 | Lumerical + Zemax |
| S5 | Meta-EE diffraction efficiency into designed orders | ≥ 70% | 55% | Lumerical RCWA |
| S6 | Order-to-order uniformity (1−σ/μ) | ≥ 90% | 80% | Lumerical RCWA |
| S7 | Reconstruction PSNR vs. target image | ≥ 25 dB | 20 dB | Python CGH |
| S8 | Speckle contrast | ≤ 0.15 | 0.25 | Python CGH |
| S9 | Virtual-image luminance | ≥ 15 000 cd/m² | 10 000 cd/m² | SPEOS |
| S10 | Ambient contrast ratio @ 15 klx | ≥ 3:1 | 2:1 | SPEOS |
| S11 | Colour gamut coverage (sRGB) | ≥ 95% | 85% | SPEOS Colorimetry |
| S12 | Eyebox luminance uniformity | ≥ 70% | 55% | SPEOS |
| S13 | Ghost / zero-order suppression | ≥ 30 dB | 20 dB | Zemax NSC |
| S14 | Min. feature size | ≥ 60 nm | 50 nm | Design rule |
| S15 | Max. aspect ratio | ≤ 12:1 | 15:1 | Design rule |

S9–S12 are the rows that almost no metasurface-display paper reports. They are the reason SPEOS is in
the programme, and they are a large part of the novelty.

---

## 2. Optical train

```
 [1] RGB laser modules 457/532/638 nm, ~50 mW combined
        |  fibre / dichroic combine
 [2] Beam expansion + collimation, top-hat shaping
        |  linear polarisation
 [3] Phase-only LCoS SLM   3840 x 2160,  p = 3.74 um,  14.362 x 8.078 mm
        |  E_SLM = 1.846 mm2·sr,  +-4.078 deg @ 532 nm
 [4] === META-EE ===  static metasurface etendue expander
        |  5 x 5 deterministic angular fan-out, super-period 3.744 um
        |  E -> ~46 mm2·sr  (x25)
 [5] Fourier relay + order-management stop  (zero-order & conjugate block)
        |
 [6] Combiner
        |   Arm A (HUD):          windshield, SPEOS HIW/HOA
        |   Arm B (direct view):  transparent diffractive screen
        |
 [7] EYEBOX  10 mm,  +-20.4 deg   ->  [8] Observer (SPEOS Human Vision)
```

---

## 3. The Meta-EE element — design derivation

This section derives the geometry from first principles so that every parameter is traceable, rather
than chosen by taste. **The derivation is what makes the element simulatable within our memory
budget — that is not a coincidence, it was designed for it.**

### 3.1 Fan-out order count and spacing

The SLM's full angular cone at 532 nm is `2 × 4.078° = 8.157°`. To **tile** the expanded field without
gaps or overlap, the fan-out orders must be spaced by exactly that cone angle:

```
Δθ = 8.157°
```

With an `N × N` fan-out, the covered half-field is `(N−1)/2 × Δθ + θ_SLM`:

| N | Covered half-field | Étendue gain |
|---|---|---|
| 3 | ±12.24° | ×9 |
| 4 | ±16.31° | ×16 |
| **5** | **±20.39°** | **×25** |
| 6 | ±24.47° | ×36 |

**N = 5 is selected** (S3, S4): it meets the ±20° FOV target almost exactly, and 25 orders keeps
per-order efficiency (~2.8% each at 70% total) within a sensible signal budget.

### 3.2 Super-period

A deflection of `Δθ = 8.157°` at λ = 532 nm requires a grating super-period

```
Λ = λ / sin Δθ = 532 nm / 0.14194 = 3749.5 nm ≈ 3.75 µm
```

**Λ ≈ the SLM pitch.** This is not a coincidence — it is the Fourier-conjugate relationship between
the SLM's sampling and its diffraction cone, and it is a useful internal consistency check.

### 3.3 Unit-cell pitch

The pillar lattice must be sub-wavelength at the *shortest* wavelength and *largest* deflection so no
unwanted propagating orders appear:

```
p < λ_min / (1 + sin θ_max) = 457 nm / (1 + sin 20.39°) = 457 / 1.3485 = 338.9 nm
```

Choose an integer number of pillars per super-period:

```
Λ / p = 3749 / 339 = 11.1   ->  take 13 pillars,  p = 3744 / 13 = 288 nm
```

> **Adopted lattice: `p = 288 nm`, super-cell = `13 × 13` pillars = `3.744 µm × 3.744 µm`.**

### 3.4 Why this geometry is the enabler

This is the decisive practical consequence:

| Simulation object | Size | Feasible? |
|---|---|---|
| One pillar unit cell (RCWA, periodic BC) | 288 nm | Trivially |
| **One full super-cell (13×13 pillars)** | **3.744 µm** | **RCWA: easily. FDTD: ~30 min** |
| 8×8 super-cells (finite-patch FDTD reference) | **30 µm** | **FDTD: ~50 GB, overnight — at our limit** |

The super-cell is small enough to be solved **rigorously and in full** — no local-periodicity
approximation needed *at the super-cell level*. And a 30 µm patch of 8×8 super-cells is exactly the
largest rigorous reference our 64 GB permits (audit §4.1).

**This is the hinge of the whole methodology.** We can compute the *exact* answer for a finite patch
and compare it against the *approximate* (LPA + tiling) answer used for the full-aperture element,
turning the hardware ceiling into the paper's central measurement (novelty claim 2).

### 3.5 Materials and layer stack

| Layer | Material | n @ 532 nm | Thickness | Note |
|---|---|---|---|---|
| Superstrate | Air | 1.000 | — | |
| Pillars | **TiO₂** (amorphous, ALD) | ≈ 2.40 | **h = 600 nm** | Low absorption across visible; the established visible-metasurface material |
| Substrate | Fused silica | 1.460 | 0.5 mm | |
| AR coat (rear) | MgF₂ / multilayer | — | — | Suppresses back-reflection ghost (S13) |

Full 2π phase coverage at the longest wavelength requires

```
h ≥ λ_max / (n_TiO2 − n_air) = 638 / (2.35 − 1) = 473 nm
```

`h = 600 nm` gives margin for dispersion engineering. With a 60 nm minimum feature (S14) the worst-case
aspect ratio is `600 / 60 = 10:1`, inside the 12:1 rule (S15) and consistent with demonstrated ALD TiO₂
processes.

**Alternative:** SiN (n ≈ 2.05) needs `h ≈ 610 nm` and is more CMOS-foundry-friendly but has lower
index contrast and therefore weaker angular performance. **Decision: TiO₂ primary, SiN as a
manufacturability sensitivity case** — a useful extra figure at low cost.

### 3.6 Unit-cell parameterisation and dispersion engineering

| Level | Cell degrees of freedom | Purpose | Cost |
|---|---|---|---|
| **L1 — baseline** | Circular pillar, radius `r` ∈ [30, 130] nm | Polarisation-insensitive; single phase vs. `r` | 1-D sweep |
| **L2 — dispersion** | Circular + hollow/annular (`r_out`, `r_in`) | 2 DOF → phase *and* phase-dispersion control → RGB achromatisation | 2-D sweep |
| **L3 — polarisation (C5 ext.)** | Rectangular pillar (`w_x`, `w_y`, rotation φ) | Full Jones matrix → polarisation-multiplexed multi-depth | 3-D sweep |
| **L4 — inverse (C10 stretch)** | Freeform topology in the super-cell | Adjoint optimisation via `lumopt2` | Expensive |

L1 → L2 is the main path. L3 and L4 are extensions with independent value.

**The achromatisation problem, stated honestly.** A single-layer metasurface's phase is
`φ(λ) = 2π (n_eff(r) − 1) h / λ`. The required fan-out phase is wavelength-independent in *angle*, so
the grating deflects each wavelength differently: at fixed Λ = 3.744 µm,

```
sin θ_1(457) = 0.1221  ->  7.01°
sin θ_1(532) = 0.1421  ->  8.17°
sin θ_1(638) = 0.1704  ->  9.81°
```

The three colour channels therefore land on **different angular grids** — a 40% spread between blue and
red. Three mitigations, to be evaluated and reported:

1. **Per-colour CGH compensation (primary).** The fan-out grid per wavelength is *deterministic and
   known*. The CGH forward model simply uses the correct grid per channel. Costs nothing optically;
   costs accuracy in the forward model — which is precisely what we can quantify.
2. **L2 dispersion-engineered cells.** Reduce, not eliminate, the spread. Realistic over a ~±10% band.
3. **Field-sequential colour.** Time-multiplex RGB; each frame uses its own grid. Robust fallback (§4.5
   of doc 02).

> **Architectural decision:** primary = (1) + (3), with (2) as an optimisation that improves
> efficiency uniformity rather than being relied upon for correctness. This is the conservative
> engineering choice and it removes achromatisation from the critical path.

### 3.7 Fan-out phase profile

The super-cell must implement a 5×5 equal-energy fan-out — the classic **Dammann / iterative
Fourier-transform (IFTA)** problem, solved here on a 13×13 grid of achievable pillar phases:

```
minimise   Σ_{m,n} ( |A_mn|² − 1/25 )²   +   λ_eff · (1 − η_total)
subject to φ_ij ∈ Φ_achievable(r),  r ∈ [30,130] nm,  i,j ∈ [1,13]
```

Note the constraint set is the **physically achievable phase set from the RCWA library**, not an
idealised continuous 0–2π. Optimising directly over realisable geometry — rather than optimising an
ideal phase and then quantising it — is a small but genuine methodological improvement, and it removes
the quantisation error that usually dominates such designs.

---

## 4. CGH engine (our original software)

### 4.1 Forward model

The essential point: the Meta-EE is **deterministic**, so it is a *known linear operator*, not noise.
For SLM phase `φ`, wavelength `λ`:

```
U_SLM(x,y)       = A_illum(x,y) · exp(i φ(x,y))
U_after(x,y)     = U_SLM(x,y) · t_meta(x,y; λ)          # t from RCWA library, complex
U_eyebox(u,v)    = F{ U_after } evaluated on the fan-out grid for λ
I(u,v)           = | U_eyebox |²
```

`t_meta` is the rigorously computed complex transmission — amplitude **and** phase, per wavelength, per
incidence angle. Using the true `t_meta` rather than an idealised one is exactly what the Lumerical
stage buys us, and the difference between the two is a headline figure.

### 4.2 Algorithms

| Algorithm | Role |
|---|---|
| **GS / GSW** (weighted Gerchberg–Saxton) | Fast baseline; good for spot arrays |
| **SGD** (stochastic gradient descent, autograd) | Primary. Differentiates through the full forward model incl. `t_meta`; directly optimises PSNR (S7) |
| **Time-multiplexed SGD** | Multiple sub-frames → speckle suppression (S8) |
| **Joint optics+CGH (C10 stretch)** | Backpropagate into pillar radii as well as SLM phase |

Implementation: **PyTorch** (autograd over complex fields, CUDA on the 8 GB Ada GPU). The SLM field is
3840×2160 complex — ~66 MB in complex64, entirely comfortable on 8 GB even with several sub-frames.

### 4.3 Speckle

Coherent illumination guarantees speckle. Reported honestly as contrast `C = σ_I / μ_I` (S8), with
mitigation by (a) `M` time-multiplexed independent CGH realisations (`C ∝ 1/√M`), and (b) modest source
bandwidth. `C ≤ 0.15` needs roughly `M ≈ 16–25` sub-frames — which is an honest and reportable cost,
and one reason the paper will not claim real-time operation.

---

## 5. Photometric budget

This budget determines whether the display is sunlight-readable, and it closes with margin.

| Stage | Transmission | Cumulative |
|---|---|---|
| RGB laser output | 50 mW | 50 mW |
| Beam shaping / collimation | 0.85 | 42.5 mW |
| SLM (fill factor × reflectivity × polariser) | 0.60 | 25.5 mW |
| **Meta-EE into designed orders (S5)** | **0.70** | **17.9 mW** |
| Relay + order-management stop | 0.85 | 15.2 mW |
| Combiner (windshield, p-pol / HOE) | 0.20 | **3.04 mW** |

Luminous flux at 532 nm (`V(λ) ≈ 0.88`):

```
Φ_v = 3.04 mW × 683 lm/W × 0.88 ≈ 1.83 lm
```

Luminance over the design étendue (`E = 29.76 mm²·sr = 29.76×10⁻⁶ m²·sr`):

```
L = Φ_v / E = 1.82 / 29.76e-6 ≈ 61 300 cd/m²
```

> **S9 requires ≥ 15 000 cd/m². The budget delivers ≈ 61 300 cd/m² — about 4× margin.**

That margin is deliberate and is spent on: the ~3× luminous-efficiency penalty of real RGB white
balance (blue and red have much lower `V(λ)`), eyebox non-uniformity (S12), and CGH diffraction
inefficiency. **Conclusion: a sunlight-readable holographic HUD closes on these numbers** — a
non-obvious result worth stating explicitly in the paper, because étendue expansion is often assumed
to be prohibitively lossy.

---

## 6. Combiner arms

### Arm A — HUD (primary; uniquely exploits SPEOS)

Windshield combiner, exploiting the installed `HOA_ANSYS.dll`, `HIW_ANSYS.dll`,
`HOA_WEDGE_ANGLE_DETERMINATION.dll`, `HOA_ASSEMBLY_TOLERANCE.dll` and
`HOA_Plugin_Virtual_Image_Export.dll`.

- Virtual image at 2–7 m, ~10° × 4°
- Windshield wedge angle solved to suppress the double-reflection ghost (dedicated plugin)
- Assembly tolerance via the HOA tolerance plugin, cross-checked in optiSLang
- Sunlight load, driver eye position, SAE compliance

### Arm B — Direct-view transparent screen (fallback, doc 02 §4.5)

Transparent diffractive screen with engineered angular scatter; Meta-EE at the projector. Preserves
the SPEOS role (ambient contrast, uniformity, colour) if Arm A stalls.

---

## 7. Error budget across tool handoffs

The paper's methodological contribution is that this table is **measured**, not asserted.

| # | Handoff | Approximation introduced | Est. error | How quantified |
|---|---|---|---|---|
| E1 | FDTD → RCWA (unit cell) | Different discretisation | < 2% | Direct comparison, same cell |
| E2 | Unit cell → super-cell | **Local periodicity (LPA)** | **5–25%, angle-dependent** | **30 µm full-wave FDTD reference — novelty claim 2** |
| E3 | Super-cell → full aperture | Periodic tiling, finite-size | < 3% | Patch-size convergence study |
| E4 | Complex `t` → Zemax surface | Sampling/interpolation of phase | < 2% | Dynamic-link vs. tabulated |
| E5 | Zemax POP → our ASM | Different propagators | < 1% | Identical test case both ways |
| E6 | Coherent → SPEOS incoherent | **Phase discarded** | **Not an error — a domain change** | Energy conservation + irradiance match |
| E7 | SPEOS BSDF quantisation | Angular binning of BSDF | < 5% | Bin-count convergence |

**E6 is the one a referee will attack**, so it is handled explicitly: SPEOS is never asked to
reconstruct a hologram. It receives (i) the Meta-EE as a **BSDF** and (ii) a source whose angular and
spectral distribution is *derived from* the coherent result. Within SPEOS the problem is genuinely
incoherent — ambient light, stray light, photometry, colour, perception — so the incoherent solver is
the *correct* physics for that stage, not a compromise.

---

## 8. Interfaces and formats

| # | From → To | Payload | Mechanism |
|---|---|---|---|
| I1 | Lumerical → Python | Complex `t(r, λ, θ, pol)` phase library | **`.h5` meta-atom database** (documented schema) via `lumapi` | ✅ |
| I2 | Lumerical → Zemax (seq.) | Metasurface as sequential surface | `.h5` index map + **User Defined surface** with `lumerical-metalens-2026R1-1.dll` (filename goes in the **Comment column**) | ✅ |
| I3 | Lumerical ↔ Zemax (NSC) | **Live** RCWA efficiency per order | `lumerical-sub-wavelength-dynamic-link-2026R1-1.dll` | ✅ **requires ZOS Premium/Enterprise** |
| I4 | Lumerical → SPEOS | Per-order efficiency vs. angle/λ | **LSWM `.lswm` (HDF5)** via `SurfaceStatePLUGIN` + `lumerical-sub-wavelength*.sop`. *Ansys recommends LSWM over BSDF for gratings* | ✅ |
| I4b | Lumerical → SPEOS | Angular scatter distribution | Speos BSDF (`.anisotropicbsdf`) from RCWA super-cell | ✅ (secondary) |
| I5 | Python CGH → Zemax | SLM phase map | Grid Phase `.DAT` | ⚠️ **custom scripting — no Ansys example** |
| I6 | Zemax → SPEOS | Geometry + optical properties + **sensors and sources** | **`.odx` (Optical Design Exchange)** | ✅ |
| I6b | Zemax ↔ SPEOS | Ray files | `.ray` / `.sdf` / `.dat` via the MIT `optical-automation` library | ✅ |
| I7 | SPEOS → analysis | Result maps, ray forensics | `.xmp`, `.lpf` | ✅ |
| I8 | Any → optiSLang | DOE parameters and responses | Workbench-level integration | ⚠️ **not documented in the optics KB — verify** |

All are backed by components verified present in `01_Capability_Audit_Installed_Stack.md`.

> **Two corrections made after the Ansys KB review**, recorded so the errors are not silently repeated:
>
> - **I6 was originally specified as `.ZRD`.** That is wrong. No Ansys documentation supports a
>   `.ZRD` path into SPEOS — `.ZRD` is a Zemax-internal ray database. **The supported route is
>   `.odx`**, which carries geometry, optical properties, sensors *and* sources.
> - **I4 was originally specified as BSDF only.** Since **2026 R1 the JSON surface format is replaced
>   by LSWM (`.lswm`, HDF5)**, and Ansys explicitly recommends LSWM over BSDF for *gratings*, because
>   BSDF lobes represent scatter less faithfully than tabulated per-order efficiency. BSDF is retained
>   as the secondary path (I4b) for the ambient/stray-light analysis where it is the right model.

---

## 9. Hard tool constraints (from the Ansys documentation review)

These are documented limits of the tools themselves, not of our design. Several directly constrain
the architecture and **one invalidates a default assumption in the original draft.**

| # | Constraint | Documented limit | Consequence for Meta-EE |
|---|---|---|---|
| **K1** | **OpticStudio POP is scalar, paraxial, TEM** | Breaks down beyond **~20° half-angle**; fails for rapidly diverging/converging beams and non-normal incidence | **Our design sits at ±20.4° — exactly at the limit.** POP therefore **cannot be the primary coherent propagator.** Our own band-limited ASM is primary; POP is a *cross-check restricted to low-angle sub-cases* |
| **K2** | **Metasurface NA ceiling** | `NA ≤ λ / (2p)` | At λ = 532 nm, p = 288 nm → **NA ≤ 0.924**. Our max deflection 20.4° → NA = 0.349. **Comfortable margin (2.6×)** |
| **K3** | **LPA breakdown** | Ansys states LPA "is most likely to break down if neighbouring meta-atoms are vastly dissimilar… if the phase response changes abruptly" | **Directly corroborates novelty claim 2.** A fan-out super-cell is precisely this worst case. The vendor flags the problem; nobody quantifies it |
| **K4** | **Boundary artefacts** | Behaviour of the outermost **up to 4 unit cells** is undefined; Ansys recommends a system aperture ~1% smaller than the physical element | Define the clear aperture 1% inside the physical element; exclude edge cells from the LPA error metric |
| **K5** | **Polarisation in ray tracing** | "Only meta-atoms insensitive to polarisation are supported by the ray-tracing" | **Constrains the C5 polarisation-multiplexed extension.** L1/L2 cells are polarisation-insensitive by design (circular/annular), so the baseline is unaffected. C5 must use full-wave + our own propagation, *not* the Zemax ray route |
| **K6** | **Full-FDTD scale** | Practical limit ≲ **100 µm radius**; ray-tracing `.h5` route supports **~10 mm radius at 64 GB** | Our 30 µm patch is conservative and safe; the `.h5` route covers the full element |
| **K7** | **LSWM characterisation cost** | Full angular/spectral characterisation of one grating "can take one to several hours" (θ:18 × φ:37 × λ:25) | Budgeted in doc 04 §14 |
| **K8** | **Licensing** | Dynamic RCWA↔Zemax link needs **ZOS Premium/Enterprise** + Lumerical FDTD ≥ 2023 R1.0 on the **same machine**; SPEOS HOA needs the **HUD Design & Analysis add-on** | **Verify entitlement in P0.2.** Both are on the critical path for Arm A |
| **K9** | **No CGH path into any Ansys tool** | No format, no example exists | The CGH layer is ours to build (already planned, doc 03 §4) |
| **K10** | **SPEOS cannot do coherent optics** | Incoherent, non-sequential ray tracing; diffraction enters only as pre-computed per-order efficiency or BSDF lobes | Already designed for (decision D6, §7 E6) |

### 9.1 The K1 correction

The original draft assumed Zemax POP would serve as the coherent propagation engine. **The Ansys
documentation's ~20° half-angle guideline makes that unsafe at our operating point.** The revised
position:

- **Primary coherent propagator: our own band-limited angular-spectrum method (ASM)** in
  Python/PyTorch — which we were building anyway for the CGH engine, and which has no paraxial
  restriction.
- **POP's role is demoted** to an independent cross-check on deliberately low-angle test cases,
  where it is valid. Error **E5** is therefore re-scoped: *"POP and ASM agree within 1% in the regime
  where POP is valid"* — which is a meaningful validation of our ASM, and an honest statement of
  POP's domain.
- Before handing any field to POP, apply far-field projection (`farfieldexact`) as Ansys recommends.

This is a good example of why the tool audit precedes the physics: the constraint was documented, and
finding it in month 0 costs nothing, whereas finding it in month 8 would have invalidated a stage.

---

## 10. Architectural decisions register

| ID | Decision | Rationale | Reversible? |
|---|---|---|---|
| D1 | RCWA for production sweeps; FDTD for validation only | 64 GB ceiling (audit §4) | No — fundamental |
| D2 | `p = 288 nm`, 13×13 super-cell, Λ = 3.744 µm | Derived §3.1–3.3; makes super-cell fully rigorous | No — defines the study |
| D3 | N = 5 fan-out (×25) | Meets ±20° FOV exactly | Yes — N is a parameter |
| D4 | TiO₂ primary, SiN sensitivity case | Visible-band performance vs. manufacturability | Yes |
| D5 | Per-colour CGH compensation, not optical achromatisation | Removes achromatisation from critical path | Yes |
| D6 | SPEOS for radiometry/perception only, never diffraction | Physics correctness; referee-proof | No — fundamental |
| D7 | Optimise over realisable phase set, not ideal-then-quantise | Removes quantisation error | Yes |
| D8 | PyTorch/CUDA for CGH | Autograd through the full forward model | Yes |
| D9 | HUD arm primary, direct-view fallback | Exploits installed HOA/HIW plugins | Yes |

> Next: `04_Simulation_Workflow_Tri_Tool.md` — the executable workflow, stage by stage.
