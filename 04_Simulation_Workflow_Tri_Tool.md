# 04 — Tri-Tool Simulation Workflow

> **Purpose:** The executable workflow. Every stage states its tool, its inputs, its outputs, its
> approximate cost on the audited hardware, and — critically — a **gate** with a numeric pass
> criterion. A stage that has not passed its gate does not feed the next stage.
>
> **Verification note.** Lumerical script commands quoted below were extracted from the installed
> `Lumerical\api\python\docs.json` (665 documented commands) and are confirmed present unless
> explicitly flagged `[CONFIRM]`.

---

## 0. Division of labour

Each tool owns a scale the others physically cannot address. This is the argument for the paper.

| Scale | Physics regime | Tool | Question answered |
|---|---|---|---|
| 10 nm – 4 µm | Rigorous vector Maxwell | **Lumerical FDTD** (+ RCWA, STACK) | What does the nanostructure *actually* do to the field? |
| 4 µm – 15 mm | Scalar/vector diffraction, coherent | **Python** (ASM + CGH), cross-checked by **Zemax POP** | What image does the hologram reconstruct? |
| 1 mm – 1 m | Geometrical + physical optics | **Zemax OpticStudio** | Does the system deliver the eyebox, FOV, tolerances — and where do ghosts come from? |
| 0.1 m – 3 m + observer | Incoherent radiometry, photometry, colour, perception | **Ansys SPEOS** | What does a human *see*, in real ambient light? |
| All | DOE, sensitivity, Monte-Carlo | **optiSLang** | Which parameters matter, and how much? |

> **The rule that keeps this defensible:** *phase* lives in Lumerical/Python/Zemax; *perception*
> lives in SPEOS. Coherent information is never passed into SPEOS, because SPEOS is an incoherent
> Monte-Carlo ray tracer. See §9.

---

## 1. Workflow map

```
 STAGE 1  Lumerical FDTD - unit cell library          --G1-->  t(r, lambda, theta, pol)
     |
 STAGE 2  Lumerical FDTD - 30 um finite-patch ref.    --G2-->  LPA error map   [NOVELTY 2]
     |
 STAGE 3  Python - super-cell fan-out (IFTA)          --G3-->  13x13 radius map
     |
 STAGE 4  Lumerical - rigorous super-cell verify      --G4-->  true 5x5 efficiencies
     |
 STAGE 5  Python/PyTorch - CGH engine (SGD)           --G5-->  SLM phase, PSNR, speckle
     |
     +----> STAGE 6  Zemax seq. - relay + eyebox      --G6-->  MTF, FOV, aberrations
     |         |
     |      STAGE 7  Zemax NSC + dynamic-link RCWA    --G7-->  ghosts, stray light  [S13]
     |
 STAGE 8  Lumerical -> BSDF export                    --G8-->  .anisotropicbsdf
     |
 STAGE 9  SPEOS - photometry, colour, human vision    --G9-->  luminance, contrast, gamut
     |
 STAGE 10 optiSLang - sensitivity + Monte-Carlo       --G10-> tolerance budget
     |
 STAGE 11 Cross-validation + provenance               --G11-> error budget table  [NOVELTY 3]
```

---

## 2. Stage 0 — Environment and provenance

**Non-negotiable, and done first.** A paper claiming a reproducible cross-tool methodology must be
able to prove which binary produced which number.

Directory layout under `9_Research/`:

```
sim/
  00_env/          environment capture, version manifest
  01_unitcell/     RCWA/FDTD sweeps -> HDF5 libraries
  02_lpa_study/    30 um FDTD references
  03_supercell/    IFTA designs
  04_cgh/          PyTorch CGH engine
  05_zemax/        .ZMX / .ZDA / ZOS-API scripts
  06_speos/        .scdocx, BSDF, .xmp, .lpf
  07_optislang/    DOE definitions
  08_validation/   cross-check harness, error budget
  09_figures/      publication figures
  common/          provenance.py, units.py, io.py
```

Every artefact carries a JSON sidecar: tool + version + build date, input hashes, solver settings,
mesh/convergence parameters, wall-clock, host fingerprint, git commit.

Version manifest to capture (all confirmed present):

| Item | Source |
|---|---|
| Ansys unified release | `v261` (2026 R1) |
| OpticStudio | `2026 R1.02` (per `MCP_VV` verified run) |
| Lumerical build | `Lumerical\bin\` build date |
| SPEOS build | `Optical Products\*\builddate.txt` |
| Interop DLL versions | `lumerical-metalens-2026R1-1.dll`, `lumerical-sub-wavelength-dynamic-link-2026R1-1.dll` |
| Python env | `pip freeze` |
| Host | i7-13850HX / 63.7 GB / RTX 2000 Ada 8 GB |

**Gate G0:** a single command regenerates the manifest and every sidecar validates against schema.

---

## 3. Stage 1 — Unit-cell library (Lumerical)

**Goal:** the complex transmission `t(r, λ, θ_inc, pol)` for the TiO₂ pillar on fused silica
(doc 03 §3.5).

### Method

Periodic/Bloch boundaries in x,y; PML in z; plane-wave injection; transmission monitor above.
Diffraction orders extracted with the **verified** grating analysis suite:

| Command | Role |
|---|---|
| `grating`, `gratingorders`, `gratingordercount` | Order amplitudes and which orders propagate |
| `gratingpolar`, `gratingangle`, `gratingvector` | Order directions |
| `gratingperiod1/2`, `gratingbloch1/2`, `gratingn`, `gratingm` | Lattice and order indexing |
| `gratingprojection` | Projected order fields |
| `stackrt`, `stackfield` | Fast transfer-matrix cross-check, AR-coat design |
| `farfield3d`, `farfieldexact3d`, `farfieldspherical` | Far-field projection |
| `addfdtd`, `addperiodic`, `addpml`, `addplane`, `addpower`, `addmesh`, `addcircle`, `addring` | Model build |
| `addsweep`, `addsweepparameter`, `addsweepresult`, `addjob` | Parameter sweeps |

> **Cost correction — important.** A single 288 nm × 288 nm × ~1.5 µm cell at a 10 nm mesh is only
> `29 × 29 × 150 ≈ 1.3 × 10⁵` Yee cells. That is **seconds per run**, not minutes. The full L1 sweep
> (40 radii × 3 λ × 9 angles × 2 pol = 2 160 runs) is therefore **a few hours across 20 cores**, and
> the 2-D L2 dispersion sweep (`r_out × r_in`) is comfortably an overnight job.
>
> This is why the memory ceiling in the audit is *not* a problem for Stage 1 — it only bites in
> Stage 2. RCWA is faster still and is the preferred production path: the **`rcwa` script command is
> documented** by Ansys (*RCWA Solver Introduction*), which **resolves the earlier `[CONFIRM]`
> flag** — `rcwa-engine.exe` is present and scriptable even though `rcwa` does not appear in the
> `lumapi` docstring set. The FDTD route remains a fully API-verified fallback, so **the programme
> does not depend on either one alone.**

### Sketch

```python
import lumapi, numpy as np, h5py

fdtd = lumapi.FDTD(hide=True)
P, H = 288e-9, 600e-9                      # lattice pitch, pillar height (doc 03 §3.3, §3.5)

def build(radius, wl, theta, pol):
    fdtd.switchtolayout(); fdtd.deleteall()
    fdtd.addrect(name="sub", x=0, y=0, x_span=P, y_span=P,
                 z_min=-1e-6, z_max=0, material="SiO2 (Glass) - Palik")
    fdtd.addcircle(name="pillar", x=0, y=0, radius=radius,
                   z_min=0, z_max=H, material="TiO2")
    fdtd.addfdtd(dimension="3D", x=0, y=0, x_span=P, y_span=P,
                 z_min=-1.2e-6, z_max=2.0e-6,
                 mesh_accuracy=4,
                 x_min_bc="Bloch", x_max_bc="Bloch",
                 y_min_bc="Bloch", y_max_bc="Bloch",
                 z_min_bc="PML",   z_max_bc="PML")
    fdtd.addplane(injection_axis="z", direction="Forward",
                  angle_theta=theta, polarization_angle=pol,
                  wavelength_start=wl, wavelength_stop=wl)
    fdtd.addpower(name="T", monitor_type="2D Z-normal", z=1.5e-6,
                  x_span=P, y_span=P)

# sweep -> complex t for the zeroth transmitted order
library = {}
for r in np.linspace(30e-9, 130e-9, 40):
    for wl in (457e-9, 532e-9, 638e-9):
        for th in np.arange(0, 41, 5):
            for pol in (0, 90):
                build(r, wl, th, pol); fdtd.run()
                fdtd.eval('t = grating("T");')       # order amplitudes
                library[(r, wl, th, pol)] = fdtd.getv("t")
```

### Outputs
`01_unitcell/library_L1.h5` — complex `t`, per-order efficiencies, plus convergence metadata.

### Gate G1

| Check | Criterion |
|---|---|
| Phase coverage over `r` | Full **0–2π** at all three λ |
| Transmission | **> 80%** across the usable radius range |
| Mesh convergence | Phase shift **< λ/50** between mesh accuracy 4 and 5 |
| `stackrt` cross-check (unstructured film) | **< 2%** — this is **E1** |
| Reciprocity / energy | `Σ orders ≤ 1`, violation **< 1%** |

---

## 4. Stage 2 — Finite-patch FDTD reference **[NOVELTY CLAIM 2]**

**This is the scientific core of the paper.** Everything else in the field assumes the local
periodicity approximation (LPA); we measure its error.

> **Vendor corroboration.** Ansys's own metalens documentation states the LPA "is most likely to
> break down if neighbouring meta-atoms are vastly dissimilar… if the phase response changes
> abruptly." A fan-out super-cell is *exactly* that worst case. The limitation is acknowledged by the
> tool vendor and quantified by nobody — which is the definition of a good target.

### Method

Simulate a genuine **finite** patch of the designed metasurface with *no* periodic boundary — PML on
all sides, a focused/finite illumination — and compare the true transmitted field against the field
predicted by tiling the Stage 1 LPA library.

| Patch | Super-cells | Extent | Est. memory | Est. time |
|---|---|---|---|---|
| A | 2×2 | 7.5 µm | ~4 GB | ~1 h |
| B | 4×4 | 15 µm | ~15 GB | ~4 h |
| C | 6×6 | 22 µm | ~32 GB | ~10 h |
| **D** | **8×8** | **30 µm** | **~50 GB** | **~16 h** |

Patch D is the hardware limit (audit §4.1). Run D overnight, one at a time, nothing else on the machine.

### The measurement

For each patch size and each **phase-gradient steepness** (i.e. each fan-out deflection angle):

```
E_LPA(θ) = || U_fullwave − U_LPA ||₂ / || U_fullwave ||₂
```

and the corresponding error in **per-order diffraction efficiency**, which is what actually matters
downstream.

**Expected result** — and the reason this is worth publishing: `E_LPA` will be small (a few %) at
shallow deflection and grow substantially (tens of %) as the phase gradient steepens, because
neighbouring pillars become strongly dissimilar and inter-cell coupling stops being negligible.
Holographic étendue expansion operates *precisely in the steep-gradient regime*, so this is not an
academic point — it determines whether the CGH pre-compensation of doc 03 §4.1 is trustworthy.

**Deliverable:** an LPA validity map over (deflection angle, λ, patch size), plus a first-order
**correction model** — e.g. an angle-dependent complex correction applied to the library — whose
residual is then re-measured. Correcting a known systematic error is a stronger contribution than
merely reporting it.

### Gate G2

| Check | Criterion |
|---|---|
| Patch-size convergence | `E_LPA` changes **< 3%** from patch C → D (this is **E3**) |
| Error map | Populated across ≥ 4 deflection angles × 3 λ |
| Correction model | Reduces mean `E_LPA` by **≥ 50%** |
| Energy conservation | **< 1%** |

---

## 5. Stage 3 — Super-cell fan-out design (Python)

IFTA/Dammann optimisation of the 13×13 radius map for a 5×5 equal-energy fan-out, optimised
**directly over the realisable phase set** from G1 (doc 03 §3.7, decision D7).

```python
# phases constrained to those physically achievable, per doc 03 D7
phi_achievable = library_L1.phase_vs_radius(wl=532e-9, theta=0)

def loss(radii):
    phi  = interp(radii, phi_achievable)         # 13x13 realisable phases
    amp  = interp(radii, library_L1.amplitude)   # amplitude matters too
    A    = fft2_orders(amp * exp(1j*phi))        # 5x5 target orders
    I    = abs(A)**2
    uniformity = ((I - I.mean())**2).sum()
    efficiency = I.sum()
    return uniformity - w_eff * efficiency
```

**Gate G3:** uniformity (1−σ/μ) **≥ 90%** (S6) and theoretical efficiency **≥ 70%** (S5), using
amplitude *and* phase from the real library — not an idealised phase-only model.

---

## 6. Stage 4 — Rigorous super-cell verification (Lumerical)

Simulate the **complete 3.744 µm super-cell** (13×13 pillars) with periodic boundaries — cheap, and
crucially **free of the LPA at the super-cell level** (doc 03 §3.4). Extract true order efficiencies
with `gratingorders` / `grating`.

**Gate G4:** rigorous per-order efficiencies agree with the Stage 3 LPA-based design within the error
bound established at G2. If not, apply the G2 correction model and re-run Stage 3. *This closed loop
between Stages 2–4 is the methodological engine of the paper.*

---

## 7. Stage 5 — CGH engine (Python / PyTorch)

Forward model exactly as doc 03 §4.1, using the **rigorous** `t_meta` from G4.

```python
import torch

def forward(slm_phase, t_meta, wl, fanout_grid):
    U = illum * torch.exp(1j * slm_phase)
    U = U * t_meta                       # rigorous complex transmission
    return torch.fft.fftshift(torch.fft.fft2(U))[fanout_grid[wl]]

phase = torch.zeros(2160, 3840, requires_grad=True, device='cuda')
opt   = torch.optim.Adam([phase], lr=0.05)
for it in range(1500):
    I = sum(abs(forward(phase, t[wl], wl, grid))**2 for wl in WLS)
    loss = -psnr(I, target) + beta * speckle_contrast(I)
    opt.zero_grad(); loss.backward(); opt.step()
```

Note the per-wavelength `fanout_grid` — this is the per-colour compensation of doc 03 §3.6 that takes
achromatisation off the critical path (decision D5).

**Key comparison figure:** reconstruction quality using (a) an *idealised* metasurface model versus
(b) the *rigorous* `t_meta`. The gap is the quantified value of doing rigorous Maxwell simulation at
all — i.e. the answer to "why does this need Lumerical?"

**Gate G5:** PSNR **≥ 25 dB** (S7); speckle contrast **≤ 0.15** with `M ≤ 25` sub-frames (S8);
demonstrated across ≥ 5 test images including a resolution chart.

---

## 8. Stages 6–7 — Zemax OpticStudio

### Stage 6 — Sequential: relay, eyebox, FOV

Metasurface inserted as a **User Defined surface** driven by the `.h5` meta-atom index map with the
installed **`lumerical-metalens-2026R1-1.dll`** (interface I2). The `.h5` filename is entered in the
surface's **Comment column** — an easily-missed convention.

- Relay and combiner design; eyebox (S2) and FOV (S3) verification
- Huygens PSF, MTF, footprint, vignetting across the eyebox
- Clear aperture set **1% inside** the physical element (constraint K4 — the outermost ~4 unit cells
  are undefined); use Entrance Pupil Diameter aperture type
- ZOS-API automation, driven via the local **`MCP_VV`** server (39 typed tools, verified against
  OpticStudio 2026 R1.02 — audit §3)

> **⚠️ POP demoted — see doc 03 §9.1 (constraint K1).** OpticStudio POP is scalar, paraxial and
> assumes TEM; Ansys documents that it **breaks down beyond ~20° half-angle**. Our design operates at
> **±20.4°**, i.e. at the limit. POP is therefore **not** the primary coherent propagator.
>
> - **Primary:** our own band-limited **ASM** (Python/PyTorch), built for the CGH engine anyway, with
>   no paraxial restriction.
> - **POP's role:** independent cross-check on deliberately **low-angle** sub-cases only.
> - Apply far-field projection (`farfieldexact`) before writing any `.ZBF` for POP, as Ansys advises.

**Gate G6:** eyebox ≥ 8 mm, FOV ≥ ±15°, and **POP vs. ASM agreement < 1% *within POP's valid
low-angle domain*** (re-scoped E5).

### Stage 7 — Non-sequential: ghosts and stray light

Uses **`lumerical-sub-wavelength-dynamic-link-2026R1-1.dll`** (interface I3) — a **live Lumerical RCWA
call during the ray trace**, so every ray's grating efficiency is rigorous at its actual local angle
and wavelength. No pre-tabulation, no interpolation error.

Targets: zero-order leakage, conjugate image, windshield double-reflection (Arm A), substrate
back-reflection, higher-order crosstalk.

**Gate G7:** ghost and zero-order suppression **≥ 30 dB** (S13); every ghost path *identified and
attributed*, not merely bounded.

> **Fallback (doc 02 §4.5):** if live co-simulation is too slow, substitute a pre-tabulated RCWA
> efficiency table vs. (angle, λ) and add the interpolation error explicitly to the budget as **E4**.

---

## 9. Stage 8 — Lumerical → SPEOS bridge

**This is the handoff a referee will scrutinise, so it is designed to be defensible.**

### 9.1 Primary route: LSWM (not BSDF)

Ansys **explicitly recommends LSWM over BSDF for gratings**, because tabulated per-order efficiency
represents a diffractive element more faithfully than fitted BSDF lobes. And **since 2026 R1 the JSON
surface format is replaced by LSWM (`.lswm`, HDF5)** — which is the release we have.

```
Lumerical RCWA/FDTD  --lswmexport-->  .lswm (HDF5)
        -> SPEOS  SurfaceStatePLUGIN,  Type = Plugin
        -> lumerical-sub-wavelength*.sop   (installed, audit §1)
        -> UV-mapping to orient the grating vector
```

Budget **1–several hours** per full angular/spectral characterisation (θ:18 × φ:37 × λ:25) — constraint K7.

### 9.2 Secondary route: BSDF

For the *ambient* and stray-light analysis — where the illumination genuinely is incoherent and broad —
a Speos BSDF (`.anisotropicbsdf`) built from the RCWA super-cell is the right model. Documented export
settings: *report grating orders* **ON**, *return theta/phi separately* **OFF**, propagation direction
**both**, backward definition **mirror k vector**.

### 9.3 What crosses the bridge, and what does not

| Transferred | Discarded |
|---|---|
| Per-order **energy** vs. incidence angle, λ, polarisation | **Phase** |
| Angular scatter distribution | Coherence |
| Spectral dependence | Interference / speckle |

Discarding phase is **not an approximation error** — it is a deliberate change of physical domain
(**E6**, doc 03 §7). SPEOS is then asked only questions that are genuinely incoherent: ambient light,
stray light, photometry, colour, perception. **The hologram is never reconstructed in SPEOS**, and no
Ansys path exists to do so even if we wanted one (constraint K10).

Two complementary source strategies:

1. **Meta-EE as an LSWM surface / BSDF** — ambient-light, stray-light and contrast analysis.
2. **Coherent result as a source** — the reconstructed angular and spectral distribution from Stage 5
   exported as a SPEOS source (ray file via the MIT `optical-automation` converter, or an intensity
   distribution), for luminance, uniformity and colour analysis.

**Gate G8:** energy conservation across the bridge **< 2%**; angular-bin convergence **< 5%** (**E7**);
SPEOS-predicted irradiance matches the Zemax NSC prediction for an identical incoherent test case
within **5%**.

---

## 10. Stage 9 — SPEOS system and perception

This stage produces the rows (S9–S12) that distinguish this paper from the field.

| Analysis | SPEOS capability (verified installed) | Spec |
|---|---|---|
| Virtual-image luminance | HUD Optical Analysis, `HOA_Plugin_Virtual_Image_Export.dll` | S9 |
| Ambient contrast @ 15 klx sun | Direct/Inverse simulation + ambient source | S10 |
| Colour gamut | `Colorimetry\`, spectral sensors | S11 |
| Eyebox luminance uniformity | Irradiance/intensity sensors over eyebox | S12 |
| Windshield wedge / ghost | `HOA_WEDGE_ANGLE_DETERMINATION.dll` | S13 cross-check |
| Assembly tolerance | `HOA_ASSEMBLY_TOLERANCE.dll` | Stage 10 input |
| Human perception | Human Vision sensor / eye model | Qualitative + CIE metrics |
| Stray-light forensics | **`.lpf` light path files** | Ray-level attribution |
| GPU acceleration | `Optis.Hybrid.CUDA.dll` on RTX 2000 Ada | Throughput |

`.lpf` forensics is worth emphasising: it lets the paper *attribute* stray light to specific surfaces
and paths rather than just reporting a contrast number — the kind of evidence that makes a referee
trust the rest.

**Gate G9:** all of S9–S12 met at threshold; every stray-light contributor above 1% attributed to a
named surface via `.lpf`.

---

## 11. Stage 10 — optiSLang sensitivity and tolerancing

Formal DOE across the whole chain — the section that is usually weakest in metasurface papers.

| Parameter | Range | Stage affected |
|---|---|---|
| Pillar radius error | ±5 nm | 1, 4 |
| Pillar height error | ±15 nm | 1, 4 |
| Sidewall angle | 0–5° | 1, 4 |
| Lattice/placement error | ±3 nm | 2 |
| Refractive index (TiO₂) | ±0.02 | 1 |
| SLM phase error | ±λ/20 | 5 |
| Element decentre / tilt | ±50 µm / ±0.1° | 6, 7 |
| Windshield shape error | ±0.2 mm | 7, 9 |
| Source wavelength drift | ±2 nm | all |
| Temperature | −40 … +85 °C | 1, 6 |

Method: Latin-hypercube DOE → metamodel (MOP) → Sobol sensitivity indices → Monte-Carlo yield.

**Gate G10:** metamodel CoP **> 90%**; the 5 dominant parameters identified; predicted yield at
threshold specs **> 80%**.

---

## 12. Stage 11 — Cross-validation and the error budget **[NOVELTY CLAIM 3]**

The error budget table of doc 03 §7 is populated with **measured** values:

| ID | Handoff | Gate | Method |
|---|---|---|---|
| E1 | FDTD ↔ STACK/RCWA unit cell | G1 | Same cell, both solvers |
| E2 | **LPA** | **G2** | **30 µm full-wave reference** |
| E3 | Tiling / finite size | G2 | Patch-size convergence |
| E4 | Lumerical → Zemax | G7 | Dynamic-link vs. tabulated |
| E5 | Zemax POP ↔ our ASM | G6 | Identical test case |
| E6 | Coherent → incoherent | G8 | Energy conservation |
| E7 | BSDF quantisation | G8 | Bin-count convergence |

Plus three independent end-to-end consistency checks:

1. **Energy audit** — total optical power conserved end to end within 3%.
2. **Analytic sanity** — the paraxial/scalar prediction reproduced in the regime where it is valid.
3. **Redundant path** — irradiance computed by Zemax NSC and by SPEOS agree within 5% for a matched
   incoherent case.

**Gate G11:** all of E1–E7 populated with numbers *and* uncertainties; all three consistency checks
passed. **This table is the paper's central methodological figure.**

---

## 13. Automation

Driven through the MCP servers already in this code base (audit §3), so the whole chain is
LLM-orchestratable:

| Tool | Server | Status |
|---|---|---|
| OpticStudio | **`MCP_VV`** (39 tools, 102 tests, verified vs. 2026 R1.02) | Ready |
| Lumerical | **`pylumerical-mcp`** (6 tools + 26 guideline topics) | Ready |
| SPEOS | *none* | **Gap — build on `SPEOS_RPC` gRPC grammar; see `08_Automation_MCP_Orchestration.md`** |
| optiSLang | Python integration | Ready |

---

## 14. Computational budget

| Stage | Cost | Notes |
|---|---|---|
| 1 — L1 unit-cell library | ~3–6 h | 20 cores, embarrassingly parallel; cells are tiny |
| 1b — L2 dispersion library | ~12–24 h | 2-D sweep, overnight |
| **2 — LPA study (patches A–D)** | **~35 h** | Sequential; D alone ~16 h at ~50 GB |
| 3 — IFTA | minutes | |
| 4 — Super-cell verification | ~1–2 h | |
| 5 — CGH (per image, SGD) | ~5–20 min | CUDA |
| 6 — Zemax sequential + POP | ~2–4 h | |
| 7 — Zemax NSC dynamic link | ~10–30 h | Live solver calls dominate |
| 8 — BSDF export | ~1–2 h | |
| 9 — SPEOS | ~10–20 h | GPU-accelerated |
| 10 — optiSLang DOE | ~40–80 h | Largest single block; metamodel reduces it |
| 11 — Validation | ~10 h | |
| **Total** | **≈ 120–200 core-hours wall-clock** | Feasible in the 18-month plan with large margin |

> Next: `05_Work_Plan_Milestones.md`.
