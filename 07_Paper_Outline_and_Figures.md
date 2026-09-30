# 07 — Paper Outline, Figures and Claims

> **Purpose:** The manuscript skeleton. Written now, before the work starts, so that every simulation
> in `04` has a figure it is meant to produce. A study that discovers its narrative afterwards
> produces a weaker paper than one that knows which figures it owes.

---

## 1. Positioning

**Working title**

> *From Maxwell to perception: rigorous multi-scale simulation of a metasurface étendue expander for
> full-colour holographic display*

Alternatives, depending on which result proves strongest:
- *Quantifying the local-periodicity approximation in steep-gradient metasurfaces for holographic étendue expansion*
- *What does idealisation cost? Physically rigorous design of metasurface étendue expanders*

**One-line positioning** (doc 06 §5):

> Prior work establishes **what pattern** an étendue expander should have. This work establishes
> **what happens when you have to build one** — and what the user finally sees.

**Target venue:** *Optics Express* (primary). Upgrade to *Optica* or *Light: Sci. Appl.* if the LPA
result is striking. See doc 06 §8.

---

## 2. The three claims and their evidence

| # | Claim | Evidence | Stage | Figure |
|---|---|---|---|---|
| **C1** | The physical-realisability gap is large and quantifiable | Reconstruction with idealised vs. rigorous `t_meta` | 5 | **F6** |
| **C2** | LPA breaks down predictably at steep phase gradients, and can be corrected | 30 µm full-wave FDTD vs. LPA prediction | 2 | **F4, F5** |
| **C3** | An end-to-end Maxwell→perception pipeline can be built and cross-validated | Error budget E1–E7 + perceptual results | 11 | **F1, F10, F11** |

Everything else in the paper supports these three.

---

## 3. Structure

### Abstract (~200 words)

Must contain, in order: the étendue wall (×16 deficit, with numbers); that expanders are established
prior art; that their design uses idealised models; that we measure what idealisation costs; the LPA
result; the perception-level closure; the headline numbers.

> **Do not lead with "étendue expander."** Referees who know Wang et al. (2026) will stop reading.
> Lead with **rigour and the realisability gap.** (doc 06 §7)

### 1. Introduction (~1.5 pages)
1.1 Holographic displays and the étendue/SBP wall — the ×16 arithmetic (doc 02 §1)
1.2 Three routes to close it; why static high-NA expansion (R3)
1.3 **Prior art, generously credited**: Kuo 2020, Tseng 2024, **Wang 2026**; Samsung R2; Stanford and Meta waveguides
1.4 **The gap**: all of it uses idealised thin-element models
1.5 Contributions (C1–C3)

### 2. Background
2.1 Étendue and space-bandwidth product
2.2 Metasurface phase control and the local-periodicity approximation
2.3 **Why LPA is suspect here** — steep, abruptly varying gradients; the vendor's own warning (K3)
2.4 Multi-scale simulation: why one tool cannot span 10 nm → 1 m → observer

### 3. Methods — the pipeline **(the methods paper within the paper)**
3.1 Four-scale division of labour (doc 04 §0) — **Fig. F1**
3.2 Unit-cell library: RCWA/FDTD, `grating*` extraction — **Fig. F2**
3.3 Finite-patch full-wave reference and the LPA harness
3.4 Super-cell design: IFTA over *realisable* phases (decision D7)
3.5 CGH engine: ASM forward model with rigorous `t_meta`; SGD
3.6 System modelling in OpticStudio; **the POP validity limit and why ASM is primary (K1)**
3.7 Photometric and perceptual modelling in SPEOS; **what is deliberately discarded at the coherent→incoherent boundary (E6)**
3.8 Cross-validation protocol and error budget
3.9 Reproducibility: versions, provenance, released package

### 4. Design
4.1 Specification and étendue target (doc 03 §1) — **Table 1**
4.2 Derivation: N = 5, Λ = 3.744 µm, p = 288 nm — **Fig. F3**
4.3 Materials, aspect ratio, manufacturability rules
4.4 Chromatic fan-out spread and per-colour compensation (doc 03 §3.6)

### 5. Results
5.1 Unit-cell library and convergence — **Fig. F2**
5.2 **LPA error map vs. deflection angle, λ, patch size** — **Fig. F4** *(claim C2)*
5.3 **Correction model and residual** — **Fig. F5** *(claim C2)*
5.4 Super-cell efficiency and uniformity — **Table 2**
5.5 **Idealised vs. rigorous reconstruction** — **Fig. F6** *(claim C1)*
5.6 Eyebox, FOV, MTF — **Fig. F7**
5.7 Ghost and stray-light attribution — **Fig. F8**
5.8 **Luminance, ambient contrast, colour gamut, uniformity** — **Fig. F9, F10** *(claim C3)*
5.9 Perceptual rendering — **Fig. F11**
5.10 Sensitivity and tolerance yield — **Fig. F12**
5.11 **Error budget** — **Table 3** *(claim C3)*

### 6. Discussion
6.1 What idealisation costs, and when it is safe
6.2 When LPA may be trusted — a practical rule for designers
6.3 Comparison with Wang 2026, Tseng 2024, Samsung, Stanford — **Table 4**
6.4 **Limitations** — no fabrication; not real-time; single architecture; POP domain
6.5 Implications for metasurface display design practice

### 7. Conclusion

### Appendices
A. Convergence studies · B. `.h5` library schema · C. LPA harness derivation ·
D. Photometric budget · E. Version manifest

---

## 4. Figures

| # | Title | Content | Source | Priority |
|---|---|---|---|---|
| **F1** | **Pipeline overview** | Four scales, tools, formats, seven handoffs with measured errors | Doc 04 §0–1 | ⭐⭐⭐ |
| F2 | Unit-cell library | Phase & transmission vs. radius, 3 λ; angular dependence; convergence inset | Stage 1 | ⭐⭐ |
| F3 | Design derivation | Étendue tiling; 5×5 fan-out; super-cell layout; chromatic grid spread | Doc 03 §3 | ⭐⭐ |
| **F4** | **LPA error map** | `E_LPA` vs. deflection angle × λ × patch size; full-wave vs. LPA field comparison | Stage 2 | ⭐⭐⭐ |
| **F5** | **LPA correction** | Correction model; residual before/after | Stage 2 | ⭐⭐⭐ |
| **F6** | **Realisability gap** | Reconstruction: idealised `t_meta` vs. rigorous `t_meta` vs. target; PSNR bars | Stage 5 | ⭐⭐⭐ |
| F7 | System performance | Eyebox map, FOV, MTF across field | Stage 6 | ⭐⭐ |
| F8 | Stray light | Ghost paths attributed via `.lpf`; suppression spectrum | Stage 7/9 | ⭐⭐ |
| F9 | Photometry | Eyebox luminance uniformity; virtual-image luminance | Stage 9 | ⭐⭐ |
| **F10** | **Ambient contrast** | Contrast vs. ambient illuminance to 15 klx; sunlight-readability threshold | Stage 9 | ⭐⭐⭐ |
| F11 | Perceptual render | SPEOS Human Vision view, day vs. night; gamut in CIE 1931 | Stage 9 | ⭐⭐ |
| F12 | Sensitivity | Sobol indices; Monte-Carlo yield | Stage 10 | ⭐ |

**Tables:** T1 specification · T2 super-cell efficiency/uniformity · **T3 error budget (E1–E7)** ·
T4 comparison with prior art.

**The four ⭐⭐⭐ figures (F1, F4, F5, F6, F10) carry the paper.** If schedule pressure forces cuts,
cut F12 then F8 — never these.

---

## 5. Writing rules

1. **Never overclaim the device.** The expander concept is Wang et al.'s. Say so in the introduction,
   plainly, and early.
2. **Lead with rigour, not étendue.** (doc 06 §7)
3. **Report the LPA result even if it is small.** A rigorous negative — "LPA is valid to X°" — is a
   genuine service to the field (risk T1).
4. **Pre-empt the SPEOS objection** in §3.7, not in a rebuttal letter (risk S4, severity 15).
5. **State "no fabrication" in the abstract or introduction**, not buried in limitations (risk S2).
6. **Every number gets an uncertainty.** This is the paper's differentiator.
7. **Attribute correctly** — Gopakumar 2024 is Stanford; Meta's is Jang/Lanman (doc 10 §6.1). Getting
   this wrong in a paper about rigour would be unfortunate.
8. **Name versions everywhere.** Ansys 2026 R1 (`v261`); the interop DLL versions; K12 version churn
   is real.

---

## 6. Reviewer objections, pre-answered

| Likely objection | Where answered |
|---|---|
| "This is Wang 2026 with extra steps" | §1.3–1.4, §6.3; abstract framing |
| "SPEOS can't do diffraction — unphysical" | §3.7, Table 3 (E6), energy conservation at G8 |
| "Simulation only, no experiment" | §1.5 scope statement; §6.4; rigour of Table 3 |
| "Why three tools?" | §2.4; Fig. F1; Fig. F6 quantifies what rigour buys |
| "LPA breakdown is already known" | §2.3 — known *qualitatively*; we map and correct it |
| "×25 is less than Tseng's ×64" | §6.3 — different claim: realisable vs. idealised |
| "Not reproducible, commercial tools" | §3.9; released package; doc 08 §6 |
| "POP is invalid at your angles" | §3.6 — **we say so first** (K1), and use ASM |

---

## 7. Secondary outputs

| Output | Content | Venue |
|---|---|---|
| **Software note** | The MCP orchestration layer (doc 08 §5) | *Optics Express* (software) / *SoftwareX* / JOSS |
| **Dataset** | Phase libraries, LPA error maps | Zenodo, with DOI |
| **Conference** | Early LPA result after M1 | SPIE Photonics West / AR-VR-MR |

Presenting the LPA result at SPIE after M1 (month 4–6) is recommended: it timestamps the contribution
against the scoop risk (S1) while the full paper is still in progress.

---

## 8. Authorship and contribution statement

To be agreed at M1. Suggested CRediT split: conceptualisation and methodology (architecture, LPA
study design); software (CGH engine, LPA harness, validation layer, MCP orchestration);
investigation (simulation campaigns); validation (cross-tool error budget); visualisation;
writing — original draft / review and editing.
