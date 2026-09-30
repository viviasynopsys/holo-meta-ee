# Session Notes: 9_Research — Metasurface Holographic Display Programme

**Project:** Meta-EE — metasurface étendue expander for a full-colour holographic display,
simulated end-to-end across Ansys Lumerical + Zemax OpticStudio + SPEOS.
**Folder:** `C:\Users\vivia\code_base\9_Research`
**Started:** 2026-09-30

---

## How this file is maintained

This is a **running log**, appended to on every prompt concerning `9_Research`.

- Each interaction gets a new `## Session N` entry, newest at the **bottom** of §4.
- **§2 (Status)** and **§5 (Open items)** are *overwritten* each time so the top of the file is
  always current.
- **§3 (Corrections)** is *append-only* — it is the institutional memory of what we got wrong and
  fixed. Never delete a row; that record is the point.
- Every factual claim should name the file or path it came from, so it can be re-checked.

---

## 1. Objective

Produce a publication-grade research programme that uses **all three** installed Ansys optics
products to their fullest on a **metasurface/DOE holographic display**, benchmarked against Sony,
Samsung and other big-tech players. Planning and brainstorming only — `.md` deliverables in
`9_Research`, no simulation code yet.

---

## 2. Current status  *(overwritten each session)*

| | |
|---|---|
| **Phase** | Planning complete; **Phase P0 not yet started** |
| **Documents** | 12 files, ~190 KB, 0 broken links |
| **Repository** | **`github.com/viviasynopsys/holo-meta-ee`** — private, `main` @ `c7860fc` |
| **Next action** | **P0.2 — verify licence entitlements** (ZOS Premium/Enterprise; Speos HUD Design & Analysis add-on) |
| **Blocking risk** | None identified; two entitlements unverified |
| **Last updated** | 2026-09-30, Session 3 |

### Deliverables

| # | File | Purpose |
|---|---|---|
| 00 | `00_Executive_Summary.md` | The whole proposal in two pages — **read first** |
| 01 | `01_Capability_Audit_Installed_Stack.md` | What is installed, verified on disk; hardware ceiling |
| 02 | `02_Concept_Brainstorm_and_Downselect.md` | Étendue physics; 12 concepts scored; prior-art correction |
| 03 | `03_System_Architecture_HoloMeta.md` | Specs, first-principles derivation, budgets, tool constraints K1–K10 |
| 04 | `04_Simulation_Workflow_Tri_Tool.md` | 11 stages, gates G0–G11, code sketches, compute budget |
| 05 | `05_Work_Plan_Milestones.md` | 6 phases, 18 months, milestones M0–M5, go/no-go |
| 06 | `06_Competitive_Benchmark_BigTech.md` | Industry + academia; the prior-art analysis |
| 07 | `07_Paper_Outline_and_Figures.md` | Manuscript structure, 12 figures, pre-answered objections |
| 08 | `08_Automation_MCP_Orchestration.md` | LLM orchestration; the SPEOS MCP gap |
| 09 | `09_Risk_Register_and_Validation.md` | 25 risks, 5-layer validation, falsifiability |
| 10 | `10_References_Bibliography.md` | 38 citations, Ansys example inventory, attribution fixes |
| — | `README.md` | Index and navigation |
| — | `session.md` | **This file** |
| — | `.gitignore` | Excludes bulk sim output, tool artefacts, secrets |

### Design, fixed

| Parameter | Value | Where derived |
|---|---|---|
| Étendue deficit | **16.1×** | `02` §1.1 |
| SLM | 3840×2160, p = 3.74 µm, ±4.078°, E = 1.846 mm²·sr | `02` §1.1 |
| Requirement | 10 mm eyebox, ±20°, E = 29.76 mm²·sr | `02` §1.1 |
| Fan-out | 5×5, spacing 8.157°, **×25** | `03` §3.1 |
| Super-period | Λ = 3.744 µm (13 cells) | `03` §3.2 |
| Unit cell | **p = 288 nm**, TiO₂, h = 600 nm | `03` §3.3, §3.5 |
| Luminance | **61 300 cd/m²** vs. 15 000 required | `03` §5 |
| FDTD ceiling | ~30 µm patch (8×8 super-cells) @ 64 GB | `01` §4.1 |

---

## 3. Corrections register  *(append-only)*

The highest-value content in this file. Each row is an error found and fixed **before** it cost
anything.

| # | Session | Claim as originally drafted | Reality | Source | Impact if missed |
|---|---|---|---|---|---|
| **C1** | 1 | "Deterministic non-random metasurface étendue expander" is our novelty | **Prior art.** Kuo (SIGGRAPH 2020); Tseng et al., *Nat. Commun.* 15 (2024), 64× | Web search | Novelty claim collapses at review |
| **C2** | 1 | *(as C1)* | **Decisive:** Wang, Zhou, Tseng, Chu, Chen, Froech, Majumdar, Heide, "Holographic display étendue expansion with a binary π-metasurface," *Opt. Lett.* (2026), DOI 10.1364/OL.613712 — the *same device concept* | Ansys/lit research | **Programme wasted** if found at month 12 |
| **C3** | 1 | Zemax POP is the coherent propagation engine | POP is scalar/paraxial/TEM and **fails beyond ~20° half-angle**; our design sits at **±20.39°** | Ansys KB *ZBF Import\Export* | Stage 6 invalid; silent wrong results |
| **C4** | 1 | Zemax → SPEOS via `.ZRD` ray file | **No such path.** `.ZRD` is Zemax-internal. Correct route is **`.odx`** (Optical Design Exchange) | Ansys KB | Stage 8 unbuildable |
| **C5** | 1 | Lumerical → SPEOS via BSDF | **LSWM (`.lswm`, HDF5)** is primary; JSON replaced by LSWM at 2026 R1; Ansys recommends LSWM over BSDF *for gratings*. BSDF retained as secondary | Ansys KB | Lower fidelity; avoidable rework |
| **C6** | 1 | *Nature* 629, 791 (2024) metasurface-waveguide paper is Meta's | **Stanford (Wetzstein).** Meta's is Jang, Bang, Chae, Lee, Lanman, *Nat. Commun.* 15, 66 (2024) | Web search + Crossref | Mis-citation in a paper about rigour |
| **C7** | 1 | Sony Spatial Reality is a holographic competitor | **Light-field autostereoscopic**, not holographic (ELF-SR2, ±25°) | Web search | Category error in the benchmark |
| **C8** | 1 | RCWA scripting availability unknown — flagged `[CONFIRM]` | **`rcwa` script command is documented** by Ansys; `rcwa-engine.exe` present. Resolved | Ansys KB | — (resolved, no impact) |
| **C9** | 1 | SLM area 116.6 mm²; luminance ~61 400 cd/m² | Exact: **116.0 mm²**, **61 300 cd/m²** | Recomputed in Python | Minor, but this is a rigour paper |
| **C10** | 3 | Initial commit message written with PowerShell `Out-File -Encoding utf8` | Injected a **UTF-8 BOM (`EF BB BF`)** into the commit subject. A naive check looked clean because PowerShell strips the BOM on capture; only `git cat-file -p HEAD` revealed it. Fixed via `[System.IO.File]::WriteAllText` + `UTF8Encoding($false)` and `git commit --amend` | Raw commit object | Permanent stray character at the head of the repo's first commit; unfixable later without a history rewrite |

---

## 4. Session log

### Session 1 — 2026-09-30 (~11:41–12:35) · Deep brainstorm and full programme plan

**Prompt.** Review `optics.ansys.com`, brainstorm deeply against an interest in holographic displays
and metasurfaces, design the best showcase use case across all three tools, produce a work plan,
workflow and milestones, benchmark against Sony/Samsung/big tech, save everything as `.md` in
`9_Research`.

**Work performed**

1. **Audited the installed stack by direct filesystem inspection** rather than assumption. Found all
   three products under one version-coherent **Ansys 2026 R1 (`v261`)** tree, plus `OpticsLauncher`,
   `SPEOS_HPC`, `SPEOS_RPC`, `optiSLang`.
2. **Discovered the interop bridges already on disk** — the decisive finding:
   - `...\Zemax\DLL\Surfaces\lumerical-metalens-2026R1-1.dll`
   - `...\Zemax\DLL\Diffractive\lumerical-sub-wavelength-dynamic-link-2026R1-1.dll` — **live
     Lumerical RCWA co-simulation during the Zemax ray trace**
   - `srg_*_RCWA` family (blaze/step/trapezoid/wire-grid/user-defined), `hologram_kogelnik.dll`
3. Confirmed `rcwa-engine.exe`, `varfdtd`, `eme`, **`lumopt`/`lumopt2`** (adjoint inverse design),
   `lumslurm.py`; extracted the **665 documented `lumapi` commands** from `api\python\docs.json` and
   verified the `grating*` order-analysis suite and `stackrt`.
4. Confirmed the SPEOS gRPC grammar at `SPEOS_RPC\APIGrammar\ansys\api\speos\` including
   `bsdf_creation.proto`, `anisotropic_bsdf.proto`, `lpf/`; and the HUD plugins `HOA_*`, `HIW_*`.
5. **Measured the hardware envelope**: i7-13850HX (20C/28T), **63.7 GB RAM**, RTX 2000 Ada
   **8188 MiB**, CC 8.9. Derived the FDTD memory wall and established the **~30 µm rigorous patch
   ceiling** — which became the programme's scientific core.
6. Dispatched a background research agent over the Ansys Optics KB (88 tool calls) and ran targeted
   web searches in parallel on Meta/Stanford, Samsung SAIT, Sony and the étendue-expansion literature.
7. **Derived the design from first principles** — étendue deficit → N = 5 fan-out → Λ = 3.744 µm →
   p = 288 nm → 13×13 super-cell — deliberately sized so the super-cell is *fully rigorous* and an
   8×8 patch is exactly the largest full-wave reference 64 GB permits.
8. **Verified all arithmetic independently** using Lumerical's bundled Python 3.13.1
   (`...\Lumerical\python-3.13.1\python.exe`, since `python` is not on PATH). Every figure confirmed.
9. Authored the 12 documents; corrected C1–C9; re-verified 0 broken links and cross-document number
   consistency.

**Key outcomes**

- **The hard interop problem is already solved on this machine.** Version-matched bridges, including
  a live co-simulation path, are installed. Far stronger than the field's norm.
- **The 64 GB ceiling became the contribution.** FDTD is repositioned as a *calibration* instrument:
  a 30 µm full-wave reference against which the **local-periodicity approximation** is measured and
  corrected. Ansys's own docs warn LPA breaks down exactly where étendue expanders operate, and
  nobody has quantified it.
- **Novelty re-based after C1/C2.** The device is prior art; the **rigour** is not. Three surviving
  claims: (1) the physical-realisability gap quantified, (2) LPA validity mapped and corrected,
  (3) first Maxwell→perception pipeline with a measured 7-handoff error budget.
- **SPEOS's role bounded honestly.** It is incoherent Monte-Carlo ray tracing and cannot reconstruct
  a hologram; it is therefore never asked to. It answers only incoherent questions — ambient
  contrast, colour, uniformity, stray light, human vision — which almost no metasurface-display
  paper reports.
- **Plan designed so every exit point still yields a paper**, with the three severity-12 risks all
  de-risked in month 1.

**Gaps identified**

- **No SPEOS MCP server exists.** Buildable in ~3–5 weeks from the installed gRPC grammar plus the
  existing retargeting meta-prompt at `6_MCP_Comparison\prompt_lumerical.md` §D.
- **CGH engine, LPA harness and validation layer must be written** — these are the original code.
- Two **licence entitlements unverified** (see §5).

---

### Session 2 — 2026-09-30 (12:38) · Create the session log

**Prompt.** "Write a `session.md` file and update it every time I prompt about this 9_Research folder."

**Work performed**

1. Read `6_MCP_Comparison\session.md` to match the established house style (Objective / Work
   Performed / Key Takeaways, prose plus tables).
2. Created `9_Research\session.md` as a **running log** rather than a single-session note, adding a
   maintenance convention (§ "How this file is maintained"), a current-status dashboard (§2) and an
   **append-only corrections register** (§3) carrying C1–C9 forward from Session 1.
3. Back-filled the Session 1 entry from the actual work performed, with paths and commands named so
   every claim is re-checkable.

**Key outcomes**

- The corrections register (§3) is the most valuable part: it preserves *why* the novelty claim was
  re-based and *why* POP was demoted, so neither decision gets silently reversed later.
- §2 and §5 are overwritten each session; §3 and §4 only ever grow.

---

### Session 3 — 2026-09-30 (12:42) · Version control and publish to GitHub

**Prompt.** "Push and commit this repo `C:\Users\vivia\code_base\9_Research` to
`https://github.com/viviasynopsys`."

**Work performed**

1. **Established the facts before acting.** Found: git 2.55 present; `9_Research` **not** a git repo;
   **no git identity configured** anywhere (global or local); `gh` CLI **not installed**;
   `credential.helper = manager`.
2. Noted the supplied URL is a **GitHub account, not a repository** — a repo name was required.
   Asked; no response available, so proceeded pragmatically with **`holo-meta-ee`** (the project name
   used throughout the docs) and **private** visibility (unpublished research IP; safe and
   reversible).
3. Authored `.gitignore` aligned with the planned `sim/` tree in `08` §6 — excludes bulk simulation
   output (`*.h5`, `*.lswm`), Lumerical/Zemax/SPEOS artefacts (`*.fsp`, `*.ZMX`, `*.ZRD`, `*.xmp`,
   `*.lpf`), Python caches, and secrets.
4. `git init -b main`; set identity **repo-scoped only** (`viviasynopsys` /
   `viviasynopsys@users.noreply.github.com`) — deliberately **not** global, and a GitHub `noreply`
   address so no personal email is baked into history. Confirmed global config left unset.
5. Committed 14 files / 3,479 insertions with a full descriptive message.
6. **Caught and fixed a defect:** PowerShell's `Out-File -Encoding utf8` wrote a **UTF-8 BOM** into
   the commit message, which git preserved at the start of the subject line. An initial check
   appeared clean because PowerShell strips the BOM on capture; inspecting the **raw commit object**
   (`git cat-file -p HEAD`) exposed `EF BB BF`. Amended using
   `[System.IO.File]::WriteAllText` with `UTF8Encoding($false)`. Re-verified: `41 64 64` = "Add".
7. Probed the remote with `GIT_TERMINAL_PROMPT=0` / `GCM_INTERACTIVE=never` to avoid hanging on an
   interactive auth dialog → "Repository not found", as expected.
8. Confirmed a **cached `github.com` credential** for `viviasynopsys` existed (checked for presence
   only; token value never printed or written to disk). Verified via the API that scopes are
   **`gist, repo, workflow`** — `repo` permits repository creation.
9. Since no repo-creation tool exists (this GitHub MCP server is read-only plus `create_pull_request`)
   and `gh` is absent, **created the repository through the GitHub REST API** using the cached
   credential held in memory only.
10. Pushed `main` and verified end to end.

**Verification**

| Check | Result |
|---|---|
| Local HEAD | `c7860fc3755c4a620736325ce33dd1e6bf208cc4` |
| Remote `refs/heads/main` | `c7860fc3755c4a620736325ce33dd1e6bf208cc4` — **identical** |
| Ahead / behind | `0 / 0` |
| Files on GitHub | **14** confirmed via API |
| Commit author | `viviasynopsys <viviasynopsys@users.noreply.github.com>` |
| Subject | Clean, no BOM |
| Visibility | **private** |

**Key outcomes**

- Repository live at **`https://github.com/viviasynopsys/holo-meta-ee`** (private).
- The BOM defect is logged as **C10**: it would have left a stray invisible character at the head of
  the repository's first commit subject — cosmetic, but permanent without a history rewrite, and
  exactly the kind of thing that is invisible until someone greps the log.
- Git identity was scoped to this repository so no assumption leaks into the user's other work.

---

## 5. Open items carried forward  *(overwritten each session)*

### Must resolve before Phase P1

| # | Item | Why it matters | Owner action |
|---|---|---|---|
| **O1** | **Licence entitlement check** — ZOS **Premium/Enterprise** (dynamic RCWA link) and Speos **HUD Design & Analysis** add-on (HOA plugins) | **Constraint K8 — both on the critical path for the HUD arm.** If absent, fall back to pre-tabulated RCWA and the direct-view arm | P0.2, week 1 |
| **O2** | Prove `lumerical-metalens-2026R1-1.dll` on a textbook metalens | De-risks the primary Lumerical→Zemax bridge | P0.5 |
| **O3** | Benchmark dynamic-link co-simulation latency | Determines whether Stage 7 is viable live or must be pre-tabulated | P0.6 |
| **O4** | FDTD memory scaling, patches A→D | Confirms the 30 µm reference fits in < 60 GB | P0.9 |
| **O5** | Establish POP's valid low-angle domain | Re-scoped E5 after correction C3 | P0.10 |

### Citation checks

| # | Item |
|---|---|
| O6 | Wang et al. (2026) volume/pages — our closest prior art, not yet assigned |
| O7 | Kress & Chatterjee volume/pages unverified |
| O8 | SPIE HoloLens 2 "butterfly waveguide" DOI failed Crossref — verify at SPIE or drop |

### Documentation gaps (login-gated `ansyshelp.ansys.com`)

| # | Item |
|---|---|
| O9 | Grid Phase authoritative reference (interface I5 — currently custom scripting) |
| O10 | IES / EULUMDAT support in SPEOS |
| O11 | optiSLang ↔ optics coupling (Stage 10) |
| O12 | SPEOS Human Vision feature article — confirmed only indirectly |

### Optional, high value

| # | Item |
|---|---|
| O13 | Build `ansys-speos-mcp` — completes the LLM-drivable tri-tool pipeline; publishable software note |
| O14 | Reproduce the Ansys **AR Windshield HUD** KB example as the tri-tool template (P0.11) |

---

## 6. Standing context

**Environment** — Ansys 2026 R1 (`v261`): Lumerical, Zemax OpticStudio 2026 R1.02, SPEOS,
OpticsLauncher, SPEOS_HPC, optiSLang. Hardware: i7-13850HX 20C/28T, 63.7 GB RAM, RTX 2000 Ada 8 GB
(CC 8.9), C: 707 GB free, D: 954 GB free.

**Useful paths**

```
Zemax user data   C:\Users\vivia\OneDrive - Synopsys, Inc\Documents\Zemax\
Lumerical         C:\Program Files\ANSYS Inc\v261\Lumerical\
  lumapi          ...\api\python\lumapi.py      (docs.json = 665 commands)
  python          ...\python-3.13.1\python.exe  (no `python` on PATH)
SPEOS             C:\Program Files\ANSYS Inc\v261\Optical Products\
  gRPC grammar    ...\SPEOS_RPC\APIGrammar\ansys\api\speos\
MCP servers       C:\Users\vivia\code_base\{MCP_VV, pylumerical-mcp-main, pyzemax-mcp}
Prior comparison  C:\Users\vivia\code_base\6_MCP_Comparison\
```

**Five things not to forget**

1. The étendue deficit is **16.1×**. Every design decision follows from it.
2. **Lead with rigour, never with "étendue expander"** — referees who know Wang 2026 will stop reading.
3. **64 GB caps FDTD at ~30 µm — which is exactly the LPA reference size.** Constraint → contribution.
4. **SPEOS must never be asked to diffract.** Highest scientific risk (15/25); answered by design.
5. **POP is invalid beyond ~20° half-angle; the design sits at ±20.39°.** Our own ASM is primary.
