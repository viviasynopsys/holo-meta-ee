# 10 — References and Bibliography

> **Status:** DOIs verified via Crossref where noted. Items marked ⚠️ carry a specific caveat that
> must be checked before citing. Items marked ⭐ are core citations for the paper.
>
> **Two attribution errors are corrected here that circulate widely in the secondary literature.**
> See §6.

---

## 1. Holographic displays and computer-generated holography

1. **An, J.**; Won, K.; Kim, Y.; Hong, J.-Y.; Kim, H.; Kim, Y.; Song, H.; Choi, C.; Kim, Y.; Seo, J.;
   Morozov, A.; Park, H.; Hong, S.; Hwang, S.; Kim, K.; Lee, H.-S.
   "Slim-panel holographic video display." *Nature Communications* **11**, 5568 (2020).
   DOI: 10.1038/s41467-020-19298-4
   — ⭐ **Samsung SAIT. The industrial baseline for true holographic display.** Steering backlight +
   HOE; ~10 mm thick; **15° viewing angle**; 150 × 90 mm backlight; 4K @ 30 fps.
   ⚠️ Lead author is **Jungkwuen An** — frequently mis-cited.

2. Gopakumar, M.; Lee, G.-Y.; Choi, S.; Chao, B.; Peng, Y.; Kim, J.; **Wetzstein, G.**
   "Full-colour 3D holographic augmented-reality displays with metasurface waveguides."
   *Nature* **629**(8013), 791–797 (2024). DOI: 10.1038/s41586-024-07386-0
   — ⭐ **THE central citation in the field.**
   ⚠️ **This is Stanford, not Meta.** See §6.1.

3. Jang, C.; Bang, K.; Chae, M.; Lee, B.; **Lanman, D.**
   "Waveguide holography for 3D augmented reality glasses."
   *Nature Communications* **15**, 66 (2024). DOI: 10.1038/s41467-023-44032-1
   — ⭐ **Meta Reality Labs' actual flagship holographic AR paper.**

4. Choi, S.; Jang, C.; Lanman, D.; Wetzstein, G.
   "Synthetic aperture waveguide holography for compact mixed-reality displays with large étendue."
   *Nature Photonics* **19**, 854–863 (2025). DOI: 10.1038/s41566-025-01718-w
   — Large-étendue mixed reality; Meta + Stanford.

5. Maimone, A.; Georgiou, A.; Kollin, J. S.
   "Holographic near-eye displays for virtual and augmented reality."
   *ACM TOG* **36**(4), Art. 85 (SIGGRAPH 2017). DOI: 10.1145/3072959.3073624

6. Maimone, A.; Wang, J. "Holographic optics for thin and lightweight virtual reality."
   *ACM TOG* **39**(4), Art. 67 (SIGGRAPH 2020). DOI: 10.1145/3386569.3392416

7. Peng, Y.; Choi, S.; Padmanaban, N.; Wetzstein, G.
   "Neural holography with camera-in-the-loop training."
   *ACM TOG* **39**(6), Art. 185 (2020). DOI: 10.1145/3414685.3417802
   — ⭐ CGH algorithm baseline for our SGD engine.

8. Choi, S.; Gopakumar, M.; Peng, Y.; Kim, J.; O'Toole, M.; Wetzstein, G.
   "Time-multiplexed Neural Holography." *ACM SIGGRAPH 2022 Conf. Proc.*, Art. 32.
   DOI: 10.1145/3528233.3530734
   — ⭐ Basis for our speckle-suppression strategy (S8).

9. Kim, J.; Gopakumar, M.; Choi, S.; Peng, Y.; Lopes, W.; Wetzstein, G.
   "Holographic Glasses for Virtual Reality." *ACM SIGGRAPH 2022 Conf. Proc.*, Art. 33.
   DOI: 10.1145/3528233.3530739 — 2.5 mm thick.

10. Shi, L.; Li, B.; Kim, C.; Kellnhofer, P.; Matusik, W.
    "Towards real-time photorealistic 3D holography with deep neural networks."
    *Nature* **591**(7849), 234–239 (2021). DOI: 10.1038/s41586-020-03152-0

11. Chang, C.; Bang, K.; Wetzstein, G.; Lee, B.; Gao, L.
    "Toward the next-generation VR/AR optics: a review of holographic near-eye displays from a
    human-centric perspective." *Optica* **7**(11), 1563–1578 (2020). DOI: 10.1364/OPTICA.406004
    — ⭐ Best review; the "human-centric" framing supports our perception-layer argument.

---

## 2. Étendue expansion — **the direct prior art**

> These four define what we may and may not claim. See `06` §5.

12. **Kuo, G.; Waller, L.; Ng, R.; Maimone, A.**
    "High-resolution étendue expansion for holographic displays."
    *ACM TOG* **39**(4) (SIGGRAPH 2020). DOI: 10.1145/3386569.3392414
    — ⭐⭐ Established optimised static scattering masks.

13. **Tseng, E.; Kuo, G.; Baek, S.-H.; … Heide, F.**
    "Neural étendue expander for ultra-wide-angle high-fidelity holographic display."
    *Nature Communications* **15** (2024). DOI: 10.1038/s41467-024-46915-3 · arXiv:2109.08123
    — ⭐⭐ **64× étendue expansion**, learned deterministic expander, full colour.

14. **Wang, C.; Zhou, Z.; Tseng, E.; Chu, V.; Chen, W.-T.; Froech, J.; Majumdar, A.; Heide, F.**
    "Holographic display étendue expansion with a binary π-metasurface."
    *Optics Letters* (2026). DOI: 10.1364/OL.613712
    — ⭐⭐⭐ **THE closest prior art.** A metasurface étendue expander for holographic display.
    **This is why the device concept is not our novelty** (doc 02 §4.3).
    ⚠️ Volume/pages not yet assigned — check before final citation.

15. Kress, B. C.; Chatterjee, I.
    "Waveguide combiners for mixed reality headsets: a nanophotonics design perspective."
    *Nanophotonics* (2020/21).
    — Best citable review of combiners and étendue. ⚠️ **Verify exact volume/pages.**

---

## 3. Metasurfaces and metalenses

16. Choi, M.; Kim, J.; Moon, S.; Shin, K.; Nam, S.-W.; Park, Y.; Kang, D.; Jeon, G.; Lee, K.-i.;
    Yoon, D. H.; Jeong, Y.; Lee, C.-K.; **Rho, J.**
    "Roll-to-plate printable RGB achromatic metalens for wide-field-of-view holographic near-eye
    displays." *Nature Materials* **24**, 535–543 (2025). DOI: 10.1038/s41563-025-02121-0
    — ⭐ **POSTECH + Samsung. Manufacturing-realism benchmark** supporting our fabrication claims.

17. Moon, S.; Kim, J.; Jo, Y.; Seo, J.; Kim, K.; Kim, S.; Lee, C.-K.; Rho, J.
    "Switchable 2D–3D display through a metasurface lenticular lens."
    *Nature* **652**, 1181–1187 (2026). DOI: 10.1038/s41586-026-10318-9

18. Kim, J.; Seong, J.; … Rho, J.
    "Scalable manufacturing of high-index atomic layer–polymer hybrid metasurfaces for metaphotonics
    in the visible." *Nature Materials* **22**, 474–481 (2023). DOI: 10.1038/s41563-023-01485-5
    — ⭐ Supports our TiO₂/high-index aspect-ratio design rules (S14, S15).

19. Li, Z.; Pestourie, R.; Park, J.-S.; Huang, Y.-W.; Johnson, S. G.; **Capasso, F.**
    "Inverse design enables large-scale high-performance meta-optics reshaping virtual reality."
    *Nature Communications* **13**, 2409 (2022). DOI: 10.1038/s41467-022-29973-3
    — ⭐ Basis for the C10 adjoint stretch goal.

20. Li, Z.; Lin, P.; Huang, Y.-W.; Park, J.-S.; Chen, W. T.; Shi, Z.; Qiu, C.-W.; Cheng, J.-X.;
    Capasso, F. "Meta-optics achieves RGB-achromatic focusing for virtual reality."
    *Science Advances* **7**(5), eabe4458 (2021). DOI: 10.1126/sciadv.abe4458
    — ⭐ RGB achromatisation reference (doc 03 §3.6).

21. Khorasaninejad, M.; Chen, W. T.; Devlin, R. C.; Oh, J.; Zhu, A. Y.; Capasso, F.
    "Metalenses at visible wavelengths." *Science* **352**(6290), 1190–1194 (2016).
    DOI: 10.1126/science.aaf6644 — ⭐ Foundational TiO₂ visible metalens.

22. Lin, D.; Fan, P.; Hasman, E.; Brongersma, M. L.
    "Dielectric gradient metasurface optical elements." *Science* **345**(6194), 298–302 (2014).
    DOI: 10.1126/science.1253213

23. Lee, G.-Y.; Hong, J.-Y.; Hwang, S.; Moon, S.; Kang, H.; Jeon, S.; Kim, H.; Jeong, J.-H.; Lee, B.
    "Metasurface eyepiece for augmented reality." *Nature Communications* **9**, 4562 (2018).
    DOI: 10.1038/s41467-018-07011-5

24. Wirth-Singh, A.; Fröch, J. E.; … Majumdar, A.
    "Wide field of view large aperture meta-doublet eyepiece."
    *Light: Science & Applications* **14**, 17 (2025). DOI: 10.1038/s41377-024-01674-0

25. Tseng, E.; Colburn, S.; Whitehead, J.; Huang, L.; Baek, S.-H.; Majumdar, A.; Heide, F.
    "Neural nano-optics for high-quality thin lens imaging."
    *Nature Communications* **12**, 6493 (2021). DOI: 10.1038/s41467-021-26443-0

26. Huang, L.; Zhang, S.; Zentgraf, T.
    "Metasurface holography: from fundamentals to applications."
    *Nanophotonics* **7**(6), 1169–1190 (2018). DOI: 10.1515/nanoph-2017-0118 — ⭐ Review.

27. Hsu, C.-Y.; et al.; Huang, Y.-W.
    "Two-Dimensional Topology Optimized Nonlocal Metasurfaces for Augmented Reality."
    *Nano Letters* **26**, 4080–4088 (2026). DOI: 10.1021/acs.nanolett.5c05872

28. Bayati, E.; Wolfram, A.; Colburn, S.; Huang, L.; Majumdar, A.
    "Design of achromatic augmented reality visors based on composite metasurfaces."
    *Applied Optics* **60**(4), 844–850 (2021). DOI: 10.1364/AO.410895

29. Yin, Y.; Jiang, Q.; Wang, H.; Huang, L.
    "Color Holographic Display Based on Complex-Amplitude Metasurface."
    *Laser & Photonics Reviews* **19**, 2400884 (2024). DOI: 10.1002/lpor.202400884

---

## 4. Simulation methodology (the lane we are entering)

30. **Leportier, T.; Bacon-Brown, D.; McGuire, D.; Gangadhara, S.; Williams, B.; George, M.; Reid, A.**
    "Novel workflow for metalens optical system design, simulation, and manufacture."
    *Proc. SPIE* **13373**, 133730R (2025). DOI: 10.1117/12.3040737
    — ⭐⭐ **The Ansys + Moxtek methodology paper. Cite this as the direct methodological predecessor**
    and state explicitly what we add (LPA quantification, realisability gap, perception layer).

31. Kress, B. C.; Cummings, W. J.
    "Towards the Ultimate Mixed Reality Experience: HoloLens Display Architecture Choices."
    *SID Symposium Digest* **48**(1), 127–131 (2017). DOI: 10.1002/sdtp.11586

---

## 5. Ansys Optics knowledge base — worked examples

> The evidence base for `04_Simulation_Workflow_Tri_Tool.md`. Root: `https://optics.ansys.com/hc/en-us`

### 5.1 Metalens / metasurface core

| Example | Relevance |
|---|---|
| **Introduction to metalens workflows** (`/articles/35797097445779`) | ⭐ Decision page: 3 tiers (full FDTD / meta-atom field stitching / ray tracing); states the **NA ≤ λ/(2·l_unit)** limit and LPA caveats |
| **Small-Scale Metalens – Field Propagation** (`/articles/360042097313`) | Canonical 5-step: Binary 1 target phase → RCWA sweep → FDTD → `.ZBF` → POP → GDS |
| **Large-Scale Metalens – Ray Propagation** (`/articles/18254409091987`) | ⭐ cm-scale: RCWA database → `.h5` → Zemax **UDS + `lumerical-metalens-XXXX.dll`**; Local Phase Gradient vs. Windowed Fourier Transform. States the **10 mm radius @ 64 GB** ceiling |
| **Achromatic Metalens** (`/articles/51430842465811`) | ⭐ Two meta-atom shapes swept for **phase slope** (dφ/df) — our L2 dispersion strategy |
| **Polarization-dependent focus metalens** (`/articles/51424535876883`) | Relevant to the C5 extension |
| **Eye tracking optical system with a metalens** (`/articles/27182763655571`) | ⭐ Most AR/VR-relevant; 16 × 32 mm eyebox |
| **Fiber Endoscope Design Using Metalens** (`/articles/33928805110163`) | Tri-tool showcase |
| **Ansys / Moxtek Meta-optics PDK** (`/articles/43826884458515`) | ⭐ **Fabrication-validated** meta-atom `.h5` libraries at 532/633 nm; cites ref. 30 |
| **HDF5 meta-atom database file format** (`/articles/30073491808147`) | ⭐ Schema — needed to *write* our own library |
| **`polystencil`** (`/articles/4401965734291`) | Fast GDS export for millions of elements |
| **RCWA Solver Introduction** (`/articles/4414575008787`); `rcwa` command (`/articles/4414567929235`) | ⭐ Confirms the `rcwa` script command |

### 5.2 Lumerical ↔ OpticStudio

| Example | Relevance |
|---|---|
| **Zemax interoperability overview** (`/articles/360034936793`) | Root page |
| **ZBF Import\Export** (`/articles/23239731653139`) | ⭐⭐ **The critical gotcha page: POP is scalar + paraxial + TEM and fails beyond ~20° half-angle.** Basis for constraint K1 |
| **Dynamic workflow between Lumerical RCWA and Zemax OpticStudio** (`/articles/42661761082899`) | ⭐⭐ Live co-simulation. **Requires ZOS Premium/Enterprise + Lumerical FDTD ≥ 2023 R1.0, same PC** |
| **LSWM plugin: Introduction and Data Generation** (`/articles/8597760630163`) | ⭐ The nano→macro surface format |
| **LSWM: Usage in Zemax OpticStudio** (`/articles/18427154870803`) | DLL location confirmed |
| **LSWM: Grating with Spatial Variations** (`/articles/34239784945299`) | Spatially-varying gratings |
| **Tabular BSDF export from Lumerical** (`/articles/50571658825619`) | Alternate route |
| **Binary 2 surface** (`/articles/42661706685075`) | `norm_radius` / `zemax_coeffs` handoff |
| **Grid Sag surface** (`/articles/43071134642323`); **phase surface on off-axis mirror** (`/articles/43071119107091`) | Confirms Grid Phase is grid-based. ⚠️ **No KB example exports a Lumerical phase map into Grid Phase — treat as custom scripting** |
| **Black Box Lens** (`/articles/43071114838675`) | `.ZBB` IP protection |

### 5.3 AR / HUD / holography

| Example | Relevance |
|---|---|
| **Augmented Reality Windshield Head-Up Display** (`/articles/44843180268179`) | ⭐⭐⭐ **The tri-tool template — closest existing analogue to our Arm A.** Zemax → `.odx` → Speos; Lumerical RCWA SRG → `.json`/LSWM → Speos. Needs **Speos ≥ 2025 R1** |
| **RGB Augmented Reality Optical System** (`/articles/33794233218579`) | 3-channel waveguide |
| **Surface Relief Grating for AR** (`/articles/15249616943123`) | RCWA SRG → LSWM → Speos |
| **Volume Holographic Grating** (`/articles/37118679173267`) | RCWA for thick VHG/HOE incl. shrinkage |
| **Kogelnik VHG diffraction efficiency** (`/articles/42661738883219`) | Matches installed `hologram_kogelnik.dll` |
| **How to model holograms in OpticStudio** (`/articles/42661708003347`) | ⚠️ **These are interference-recorded HOE surfaces, NOT CGH** |
| **Modelling a holographic waveguide, parts 1–2** (`/articles/42661776136723`, `/42661765052051`) | Full waveguide design |
| **EPE with diffractive optics, parts 3–4** (`/articles/42661798799251`, `/42661810986003`) | Pupil expansion |
| **Head-Up Display** (`/articles/10072384186899`) | ⭐ OpticStudio backward design → CAD → **Speos HOA** → driver view + ghosts |
| **HUD: from OpticStudio to Speos** (`/articles/42712682301075`) | Export mechanics |

### 5.4 Speos

| Example | Relevance |
|---|---|
| **Speos Interoperability Overview** (`/articles/7159479892371`) | Root |
| **LSWM plugin: Usage in Speos** (`/articles/13240235894035`) | ⭐⭐ `SurfaceStatePLUGIN` → Type **Plugin** → `.sop` + LSWM. **JSON replaced by LSWM (h5) at 2026 R1** |
| **Speos BRDF/BTDF/BSDF Formats** (`/articles/18384793374227`) | `.brdf` (spectral, no anisotropy) vs `.anisotropicbsdf` |
| **Speos BSDF export from Lumerical RCWA** (`/articles/53114778588307`) | ⭐ Exact export settings; **states LSWM is preferred over BSDF for gratings** |
| **Diffuse Scattering Film for Automotive Display** (`/articles/6866903993619`) | FDTD → BSDF → Speos |
| **Optical Design Exchange (ODX)** (`/articles/54950440690451`) | ⭐⭐ **The Zemax→Speos route** — geometry + properties + sensors + sources |
| **Planar OLED Human Vision Speos Interoperability** (`/articles/8314838263699`) | ⭐ Human Vision sensor + emissive display |
| **Stray Light Analysis Overview** (`/articles/45146457845395`) | ODX → Speos stray light |
| **Speos Sensor System Exporter** (`/articles/20349875326611` et al.) | Camera sensor + Lumerical QE |

### 5.5 Automation

| Resource | Relevance |
|---|---|
| **Optical Automation library** (`/articles/24752772596371`) · https://github.com/ansys/optical-automation | ⭐⭐ **MIT-licensed, cross-tool (Speos + Zemax + Lumerical), includes a rayfile converter (`.ray`/`.sdf`/`.dat`)**. ⚠️ Speos GUI scripting is **IronPython**; external automation is **CPython** |
| **PySpeos / `ansys-speos-core`** | https://speos.docs.pyansys.com · https://github.com/ansys/pyspeos — ⭐ gRPC **headless** Speos |
| **ZOS-API using Python.NET** (`/articles/42661747915539`) | pythonnet → ZOS-API |
| **Getting Started with lumopt** (`/articles/360050995394`) | ⭐ Adjoint inverse design (C10) |
| **`lswmexport`** (`/articles/13817263692563`) | Scripted LSWM export |

---

## 6. Attribution corrections

Two errors circulate widely and must not appear in our manuscript.

### 6.1 The *Nature* 2024 metasurface-waveguide paper is **Stanford, not Meta**

*Nature* **629**, 791–797 (2024) — Gopakumar et al. — is led by **Gordon Wetzstein's group at
Stanford**. **There is no Meta-authored *Nature* 2024 metasurface-waveguide AR paper.** Meta Reality
Labs' flagship holographic AR work is ref. 3 (Jang, …, Lanman, *Nat. Commun.* **15**, 66, 2024) and
ref. 4 (*Nat. Photonics* **19**, 854, 2025). Citing ref. 2 as "Meta's paper" is a common and
checkable error.

### 6.2 "Holographic" vs. light field

**Sony's Spatial Reality Display (ELF-SR2) is an eye-tracked autostereoscopic light-field display,
not a hologram** — 27″ 4K LCD + lenticular micro-optical layer + high-speed eye tracking;
±25° horizontal viewing. Useful as a *commercial viewing-angle and colour-gamut benchmark*, never as
a holographic competitor (doc 06 §1.1).

---

## 7. Grey literature / industry

32. **Metalenz** — Polar ID / PolarEyes. https://metalenz.com/polareyes-polarization-imaging-system/
    (UMC 40 nm CMOS, 300 mm wafer-level). *Evidence that metasurfaces ship at consumer volume.*
33. **NIL Technology** — metalenses, nanoimprint. https://www.nilt.com/technology/metalenses/
    (*metaEye* 1.7 mm TTL eye-tracking module). *Manufacturing route for our design rules.*
34. **Lumotive** — Light Control Metasurface. https://lumotive.com/technology/
    *The only shipping electronically reconfigurable metasurface.*
35. **Magic Leap 2** — https://www.magicleap.com/magic-leap-2 (70° diagonal, segmented dynamic
    dimming). *Ambient-contrast benchmark for S10.*
36. **Sony Spatial Reality Display ELF-SR2** — https://pro.sony/en_CA/products/spatial-reality-displays/elf-sr2
37. **Envisics** — automotive holographic HUD. *Closest commercial analogue to Arm A.*
38. **VividQ** — CGH software for AR/automotive.

⚠️ **Do not cite without checking:** "Butterscotch Varifocal" has **no DOI** (SIGGRAPH 2023 E-Tech
demo + Meta blog only). Sony ECX micro-OLED specifications circulate from teardowns, **not Sony
datasheets**. The SPIE HoloLens 2 "butterfly waveguide" DOI failed Crossref resolution — verify at
SPIE directly.

---

## 8. Open verification items

Carried into P0 (doc 05 §2):

| Item | Why it matters | Action |
|---|---|---|
| Ref. 14 (Wang 2026) volume/pages | Our closest prior art | Check Optics Letters on submission |
| Ref. 15 (Kress & Chatterjee) volume/pages | Review citation | Verify at *Nanophotonics* |
| **Licence entitlement: ZOS Premium/Enterprise + Speos HUD Design & Analysis** | **Constraint K8 — critical path for Arm A** | **P0.2** |
| IES / EULUMDAT support in Speos | Source import option | Check `ansyshelp.ansys.com` (login-gated) |
| optiSLang ↔ optics coupling | Stage 10 | Not in the optics KB; Workbench-level research |
| Grid Phase authoritative reference | Interface I5 | `ansyshelp.ansys.com` (login-gated) |
| Speos Human Vision feature article | Stage 9 | Confirmed indirectly only |
