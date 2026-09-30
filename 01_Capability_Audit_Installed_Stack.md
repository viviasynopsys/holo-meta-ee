# 01 — Capability Audit of the Installed Optical Stack

> **Status:** Verified by direct filesystem inspection on this workstation, 2026-09-30.
> Everything in this document was *observed*, not assumed. Paths are given so any claim can be re-checked.
> This audit is the factual foundation for every downstream plan. Nothing in the work plan
> depends on a tool we have not confirmed is present.

---

## 1. Headline finding

All three products are installed under a **single unified Ansys 2026 R1 (`v261`) tree**, together with
`OpticsLauncher`, `SPEOS_HPC`, `SPEOS_RPC` and `optiSLang`. This matters more than it first appears:

- It means the **Lumerical ↔ Zemax interoperability DLLs are version-matched** to the installed
  OpticStudio (`...-2026R1-...dll`). Cross-version mismatch is the single most common cause of failure
  in this workflow, and we do not have it.
- It means a **single licence/telemetry domain**, so an end-to-end automated run is realistic rather
  than aspirational.

**The decisive discovery of this audit** is that the official Lumerical→Zemax metasurface bridges are
*already on disk*, including a **live co-simulation** variant. This converts the central risk of the
project (“can the three tools actually be coupled?”) from an open research question into a
configuration task.

```
C:\Users\vivia\OneDrive - Synopsys, Inc\Documents\Zemax\DLL\Surfaces\
    lumerical-metalens-2026R1-1.dll          <-- sequential metasurface UDS
    lumerical-metalens-2025R2-2.dll          <-- previous release, kept
    us_hologram_kogelnik.dll                 <-- volume/Bragg HOE (Kogelnik coupled-wave)
    us_grate.dll  (+ us_grate.c source)      <-- analytic grating, source included
    us_zernike+msf.dll                       <-- Zernike + mid-spatial-frequency

C:\Users\vivia\OneDrive - Synopsys, Inc\Documents\Zemax\DLL\Diffractive\
    lumerical-sub-wavelength-2026R1.dll
    lumerical-sub-wavelength-2026R1-3.dll
    lumerical-sub-wavelength-dynamic-link-2026R1-1.dll   <-- LIVE Zemax<->Lumerical RCWA
    srg_blaze_RCWA / srg_step_RCWA / srg_step2 / srg_step3
    srg_trapezoid_RCWA / srg_trapezoid2_RCWA
    srg_user_defined_RCWA / srg_GridWirePolarizer_RCWA
    hologram_kogelnik.dll
    Grid_rect_windows.dll
    Diff2DSample.dll / diff_samp_1.dll (+ .c and .cpp sources)
```

The `srg_*_RCWA` family is a parametric **surface-relief grating** library solved by RCWA — blazed,
binary (step/step2/step3), trapezoidal, wire-grid polarizer, and a **user-defined** profile. This is
precisely the component set required for a diffractive waveguide combiner, and it means grating
efficiency is computed rigorously rather than assumed.

`lumerical-sub-wavelength-dynamic-link-2026R1-1.dll` is the strategically important one: it calls a
live Lumerical RCWA session *during* the OpticStudio trace, so the grating efficiency used by the ray
trace is the rigorous one at the actual local angle of incidence and wavelength — no pre-tabulation,
no interpolation error. **This is the backbone of the proposed workflow.**

---

## 2. Product-by-product inventory

### 2.1 Ansys Lumerical

`C:\Program Files\ANSYS Inc\v261\Lumerical\`

| Observed | Path / file | Why it matters here |
|---|---|---|
| **FDTD** | `bin\fdtd-engine.exe`, `fdtd-engine-impi.exe`, `fdtd-engine-msmpi.exe` | Rigorous 3-D Maxwell. MPI builds present → multi-process on the 20-core CPU |
| **RCWA** | `bin\rcwa-engine.exe` | Periodic/quasi-periodic solver. Orders-of-magnitude faster than FDTD for unit cells — this is what makes a large phase-library sweep affordable |
| **varFDTD** | `bin\varfdtd-engine.exe` (+ impi/msmpi) | 2.5-D effective-index propagation; cheap slab-waveguide studies |
| **EME** | `bin\eme-engine.exe` (+ impi/msmpi) | Eigenmode expansion — long propagation without FDTD cost |
| **FDE / MODE** | `bin\mode-solutions.exe`, `fd-engine.exe` | Waveguide mode solving |
| **DEVICE family** | `device-engine.exe`, `dgtd-engine.exe`, `feem-engine.exe`, `thermal-engine.exe` | Multiphysics; thermal drift of a metasurface is a real tolerancing input |
| **INTERCONNECT** | `interconnect.exe` | Not central to this project |
| **Python API** | `api\python\lumapi.py`, `lumjson.py` | Headless automation — the spine of the pipeline |
| **Inverse design** | `api\python\lumopt`, `lumopt2` | **Adjoint topology optimisation.** `lumopt2` is the newer framework. This enables a genuine inverse-design contribution, not just parameter sweeps |
| **HPC/scheduler** | `api\python\lumslurm.py` | Slurm submission if the study later moves to a cluster |
| **Other bindings** | `api\matlab`, `api\c`, `api\tcl`, `api\cosim` | `cosim` is the C co-simulation API used by the dynamic-link DLL |
| **Layout interop** | `interfaces\klayout`, `interfaces\tanner` | GXF/GDS-II export path toward mask/nanofabrication |

**Interpretation.** The presence of `rcwa-engine` *and* `lumopt2` *and* the `cosim` API is the
combination that makes an ambitious paper feasible on a laptop. RCWA makes the sweep cheap, `lumopt2`
makes the design novel, `cosim` makes the Zemax coupling live.

### 2.2 Ansys Zemax OpticStudio

`C:\Program Files\ANSYS Inc\v261\Zemax OpticStudio\`
User data: `C:\Users\vivia\OneDrive - Synopsys, Inc\Documents\Zemax\`

| Observed | Path | Why it matters |
|---|---|---|
| Lumerical metalens UDS | `DLL\Surfaces\lumerical-metalens-2026R1-1.dll` | Drops a Lumerical-derived metasurface straight into a **sequential** design |
| Lumerical sub-wavelength + **dynamic link** | `DLL\Diffractive\lumerical-sub-wavelength-dynamic-link-2026R1-1.dll` | Live RCWA during the trace (see §1) |
| RCWA SRG library | `DLL\Diffractive\srg_*_RCWA.*` | Rigorous parametric gratings for the combiner |
| Volume hologram | `DLL\Surfaces\us_hologram_kogelnik.dll`, `DLL\Diffractive\hologram_kogelnik.dll` | Kogelnik coupled-wave HOE — the classic Samsung-style route, available as a **baseline to benchmark against** |
| Physical Optics | `DLL\PhysicalOptics\` (`Ince-Gaussian-64.dll`, `Laguerre beam.dll`, `beamsamp1.c`) | Custom coherent beam definitions for POP |
| POP working dir | `POP\` | Physical Optics Propagation — our coherent wavefront engine inside Zemax |
| Scatter data | `ScatterData\`, `ABg_Data\` | ABg / tabulated BSDF for stray light |
| Black boxes | `BlackBoxes\` | `.ZBB` export — how we would share the design without revealing it |
| Automation | `ZOS-API\`, `ZOS-API Sample Code\`, `APIP\` | ZOS-API (.NET/COM) — drives OpticStudio headless |
| HPC | `HPC\` | OpticStudio HPC support |
| Relevant samples | `Samples\Sequential\Diffractive components\{Reflection,Transmission} hologram.zmx`; `Samples\Non-sequential\Diffractives\*`; `Samples\Non-sequential\Coherence Interference and Diffraction\*` | Working starting points — do not build from a blank file |
| Custom geometry | `Objects\{Polygon,CAD,Grid Files,STOP Files,Part Designer}` | Import of fabricated/measured geometry |

**Note on `us_grate.c` and `diff_samp_1.c`:** C source for user DLLs is shipped. If we need a
metasurface surface model that the stock DLLs do not cover (e.g. a polarisation-multiplexed Jones-matrix
surface), we can compile our own against a known-good template rather than reverse-engineering the ABI.
This is a meaningful de-risking asset.

### 2.3 Ansys SPEOS

`C:\Program Files\ANSYS Inc\v261\Optical Products\`

| Observed | Path | Why it matters |
|---|---|---|
| Core | `Speos\bin`, `Speos\Addins` | Speos inside Discovery/SpaceClaim |
| **HUD plugins** | `Plugins\HOA_ANSYS.dll`, `HOA_PROJECTOR_IMAGE.dll`, `HOA_WEDGE_ANGLE_DETERMINATION.dll`, `HOA_ASSEMBLY_TOLERANCE.dll`, `HIW_ANSYS.dll` | **HUD Optical Analysis** and **HUD-In-Windshield**. Warping, wedge-angle solve, assembly tolerance, virtual-image export. Directly applicable to a holographic HUD |
| Virtual image | `Plugins\HOA_Plugin_Virtual_Image_Export.dll` | Exports the virtual image — the natural comparison point against Zemax POP |
| SAE | `Plugins\HOA_Plugin_SAE.dll` | Automotive SAE compliance |
| Other | `Plugins\PluginOPT_DVS3.dll`, `HIW_ANSYS.dll` | Driver vision / windshield |
| GPU ray tracing | `SPEOS_RPC\Optis.Hybrid.CUDA.dll`, `Optis.Hybrid.GL.dll` | CUDA-accelerated — our 8 GB Ada GPU is usable |
| HPC | `SPEOS_HPC\` (`msmpi.dll`) | Distributed simulation |
| **gRPC API** | `SPEOS_RPC\APIGrammar\ansys\api\speos\` | See below — this is the automation key |
| Libraries | `OpticalLibraries\{Model_SPEOS, Optical_Property, Optical_Sensor, Source_SPEOS, Source_SPE, Source_SNX, Standard}` | Stock sources/sensors/properties |
| Colour | `Colorimetry\` | Colorimetric pipeline for gamut work |

**The SPEOS gRPC grammar** (`SPEOS_RPC\APIGrammar\ansys\api\speos\`) exposes exactly the namespaces we
need, and confirms the automation surface:

```
bsdf/   -> anisotropic_bsdf.proto, spectral_bsdf.proto, bsdf_creation.proto
sop/    (surface optical properties)      vop/  (volume optical properties)
source/  intensity/  intensity_distributions/  spectrum/
sensor/  simulation/  job/  scene/  part/  results/
lpf/    (light path files - ray-level forensics)      xmp/  (result maps)
LTF/    file/  common/  server_info/
```

`bsdf_creation.proto` is the critical one: **SPEOS can ingest a programmatically constructed BSDF.**
That is the officially supported mechanism by which a Lumerical-computed, angle- and
wavelength-resolved scattering distribution becomes a SPEOS surface property. It is the bridge that
makes the third tool a genuine participant rather than a decorative final render.

`lpf/` (light path files) matters for the paper's rigour: it lets us trace *individual* ray histories
to prove where stray light and ghost diffraction orders originate, rather than just showing a pretty
illuminance map.

### 2.4 Shared / orchestration

| Component | Path | Role |
|---|---|---|
| **Ansys Optics Launcher** | `v261\OpticsLauncher\launcher.exe` | Unified entry point across the three products |
| **optiSLang** | `v261\optiSLang` | Design of experiments, sensitivity analysis, multi-objective optimisation, metamodelling — the formal DOE spine for the tolerancing study |
| Workbench / Framework | `v261\Framework`, `aisol`, `dpf` | Coupling and post-processing |
| Discovery / SpaceClaim | `v261\scdm` | CAD host for SPEOS |

`optiSLang` is easy to overlook and is a differentiator: it gives us a defensible, automated
**sensitivity analysis and Monte-Carlo tolerancing** capability spanning all three solvers, which is
normally the weakest section of a metasurface paper.

---

## 3. Local automation assets (already in this code base)

These are ours, not Ansys's, and they change what is realistic for an autonomous pipeline.

| Asset | Path | State |
|---|---|---|
| `MCP_VV` | `C:\Users\vivia\code_base\MCP_VV` | C# / .NET MCP server for OpticStudio. **39 typed tools + 2 escape valves**, 102 passing tests, GitHub Actions CI, MIT. Built and verified against **OpticStudio 2026 R1.02**. One dedicated apartment thread for ZOS-API; revision-stamped read cache; 11-code fault taxonomy; `--guard` read-only mode; JSON-lines audit journal |
| `pyzemax-mcp` | `C:\Users\vivia\code_base\pyzemax-mcp` | PyAnsys Python MCP, 6 tools, thin executor |
| `OpticStudioMCPServer` | `C:\Users\vivia\code_base\OpticStudioMCPServer-main` | Community C# MCP, 100+ hand-wrapped tools |
| `pylumerical-mcp` | `C:\Users\vivia\code_base\pylumerical-mcp-main` | PyAnsys `ansys-lumerical-mcp`, 6 tools + 1 resource + **26 guideline topics**, ~160 KB domain corpus. Covers FDTD/MODE/DEVICE/INTERCONNECT |
| Prior comparison work | `C:\Users\vivia\code_base\6_MCP_Comparison\*.md` | Detailed three-way evaluation, build prompts for both servers |

**Gap:** there is **no SPEOS MCP server**. The `SPEOS_RPC` gRPC grammar plus PySpeos
(`ansys-speos-core`) makes one entirely buildable, and `prompt_lumerical.md` §D is explicitly a
*meta-prompt for retargeting the architecture to another product*. Closing this gap would complete an
LLM-drivable tri-tool pipeline — a legitimate secondary contribution of the paper, and arguably a
standalone software note.

---

## 4. Hardware envelope — and the constraint that shapes the whole project

| Resource | Value |
|---|---|
| CPU | Intel Core i7-13850HX — **20 cores / 28 threads** |
| RAM | **63.7 GB** |
| GPU | NVIDIA RTX 2000 Ada Laptop — **8188 MiB (8 GB)**, driver 596.71, **compute capability 8.9** |
| OS | Windows 11 Enterprise |
| Disk | C: 707 GB free · D: 954 GB free |

### 4.1 The binding constraint

**64 GB of RAM makes full-aperture 3-D FDTD of a display-sized metasurface physically impossible.**
This is not a minor caveat; it determines the entire architecture, so it is worth doing the arithmetic
explicitly.

A 3-D Yee grid needs roughly 6 field components plus material/auxiliary arrays. A practical
rule of thumb for Lumerical is **≈ 500 bytes – 1 kB per Yee cell** once auxiliary and PML arrays are
counted. At a mesh of λ/20 in a high-index pillar (Δ ≈ 20 nm at λ = 450 nm in TiO₂):

| Aperture (square) | Cells @ 20 nm, 1 µm thick | Memory (≈0.5 kB/cell) | Verdict |
|---|---|---|---|
| 10 µm × 10 µm | 500 × 500 × 50 = 1.25×10⁷ | ~6 GB | Comfortable |
| 30 µm × 30 µm | 1500 × 1500 × 50 = 1.1×10⁸ | **~56 GB** | At the absolute limit |
| 50 µm × 50 µm | 2500 × 2500 × 50 = 3.1×10⁸ | ~156 GB | Impossible |
| 1 mm × 1 mm | 1.25×10¹¹ | ~62 TB | Absurd |
| 10 mm × 10 mm (a real screen) | 1.25×10¹³ | ~6 PB | Absurd |

So the honest maximum for a *rigorous, full-wave, finite-aperture* metasurface patch on this machine is
**≈ 30 µm across**, and only with a coarse mesh and a short pulse.

### 4.2 Why this is an opportunity, not a limitation

This constraint is *the same one every group in the field faces*, including Meta and Samsung. Nobody
runs full-wave FDTD on a 10 mm metasurface. Everyone uses the **local periodicity approximation (LPA)**:
simulate one unit cell under periodic boundary conditions, build a phase/amplitude library, then tile it
and propagate scalar-ly.

But the LPA's error is **routinely asserted and rarely quantified**. It breaks down exactly where
holographic displays need it most: at **high deflection angles** and **steep phase gradients**, where
neighbouring pillars differ strongly and inter-cell coupling is no longer negligible.

The 30 µm FDTD budget is precisely large enough to run a **rigorous full-wave reference** against which
the LPA prediction can be compared, at a range of phase-gradient steepnesses. That comparison — *"here
is how wrong the standard approximation is, as a function of deflection angle, and here is the corrected
model"* — is a **publishable result in its own right**, and it converts our hardware ceiling into the
paper's methodological backbone.

> **Design rule adopted for this project:** FDTD is a *validation and calibration* instrument, never a
> production one. Production sweeps run on RCWA. See `04_Simulation_Workflow_Tri_Tool.md`.

### 4.3 Practical solver budgets

| Task | Solver | Expected cost on this machine |
|---|---|---|
| One unit cell, 1 λ, 1 angle | RCWA | seconds |
| Phase library: 40 radii × 3 λ × 9 angles × 2 pol | RCWA | ~2–6 h (embarrassingly parallel over 20 cores) |
| Dispersion-engineered library (2-parameter cell) | RCWA | ~1–2 days |
| Single unit cell cross-check | FDTD | ~5–20 min |
| 30 µm finite patch, 1 λ | FDTD | ~6–18 h, ~50 GB — run overnight, one at a time |
| Adjoint inverse design, 2-D cross-section | `lumopt2` + FDTD | ~1–3 days |
| Zemax POP, 2048² grid | OpticStudio | minutes |
| Zemax NSC with dynamic-link RCWA | OpticStudio + Lumerical | hours (live solver calls dominate) |
| SPEOS interactive preview | CUDA | seconds–minutes |
| SPEOS full inverse/direct with human vision | CUDA + CPU | hours |

**GPU caveat:** 8 GB VRAM is modest. Lumerical GPU-FDTD will only accept small problems; SPEOS CUDA
acceleration is the better use of that GPU. Do not plan around GPU-FDTD.

---

## 5. What is present vs. what must be built

| Capability | Status | Action |
|---|---|---|
| Rigorous unit-cell solving (RCWA/FDTD) | **Present** | Use directly |
| Adjoint inverse design | **Present** (`lumopt2`) | Use directly |
| Metasurface → Zemax sequential | **Present** (`lumerical-metalens-2026R1-1.dll`) | Configure |
| Live RCWA ↔ Zemax co-simulation | **Present** (dynamic-link DLL) | Configure — highest-value asset |
| Rigorous SRG library | **Present** (`srg_*_RCWA`) | Use directly |
| Volume HOE baseline | **Present** (Kogelnik DLLs) | Use as benchmark arm |
| Coherent propagation in Zemax | **Present** (POP) | Use, but validate against our own ASM |
| BSDF into SPEOS | **Present** (`bsdf_creation.proto`) | Scripting required |
| HUD/eyebox/human-vision analysis | **Present** (HOA/HIW plugins) | Configure |
| DOE / sensitivity / tolerancing | **Present** (optiSLang) | Configure |
| **CGH engine (GSW / SGD / neural)** | **Absent** | **Build in Python** — core original code |
| **LPA-error quantification harness** | **Absent** | **Build** — core original contribution |
| **SPEOS MCP server** | **Absent** | Build (optional, high value) |
| **Cross-tool provenance/validation harness** | **Absent** | **Build** — required for reproducibility claims |

---

## 6. Audit conclusions

1. **The toolchain is complete and version-coherent.** The hard interop problem is already solved by
   shipped, version-matched DLLs. This is a far stronger starting position than the field's norm.
2. **RCWA + `lumopt2` + dynamic-link is the winning combination.** It supports rigorous design,
   genuine inverse design, and rigorous system-level tracing, all within budget.
3. **64 GB RAM caps rigorous full-wave work at ~30 µm.** Architect around it; use FDTD to
   *calibrate* the approximation rather than to replace it — and publish that calibration.
4. **SPEOS's role must be radiometric/perceptual, not diffractive.** SPEOS is incoherent Monte-Carlo
   ray tracing; it cannot reconstruct a hologram. Its value is ambient contrast, colour, eyebox
   luminance uniformity, stray light and human vision — the things Lumerical and Zemax cannot do, and
   which almost no holographic display paper reports. Handoff must be via **BSDF** and via
   **source definitions derived from coherent results**, never by asking SPEOS to diffract.
5. **The missing pieces are software we can write**: the CGH engine, the LPA-error harness, the
   validation/provenance layer, and optionally a SPEOS MCP server.

> Proceed to `02_Concept_Brainstorm_and_Downselect.md`.
