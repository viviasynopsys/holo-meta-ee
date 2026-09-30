# 08 — Automation and MCP Orchestration

> **Purpose:** Define how the tri-tool pipeline is driven programmatically, and how the MCP servers
> already in this code base turn it into an LLM-orchestratable workflow.
>
> **Secondary thesis:** the orchestration layer is itself a publishable contribution — a software
> note or a methods section — because *nobody has demonstrated an agent-driven, provenance-tracked,
> cross-vendor optical simulation chain spanning nanophotonics, lens design and photometry.*

---

## 1. Current state

From the audit (§3), three of the four tools already have MCP servers in this code base:

| Tool | Server | Location | Surface | Maturity |
|---|---|---|---|---|
| **OpticStudio** | **`MCP_VV`** | `C:\Users\vivia\code_base\MCP_VV` | **39 typed tools + 2 escape valves** | **High** — 102 tests, CI, MIT, verified against OpticStudio 2026 R1.02 |
| OpticStudio (alt) | `pyzemax-mcp` | `C:\Users\vivia\code_base\pyzemax-mcp` | 6 tools (thin executor) | PyAnsys, Apache-2.0 |
| OpticStudio (alt) | `OpticStudioMCPServer` | `...\OpticStudioMCPServer-main` | 100+ hand-wrapped tools | Community |
| **Lumerical** | **`pylumerical-mcp`** | `...\pylumerical-mcp-main` | 6 tools + 1 resource + **26 guideline topics** (~160 KB corpus) | PyAnsys, Apache-2.0 |
| **SPEOS** | — | — | — | **GAP** |
| optiSLang | — | Python integration available | — | Direct scripting |

### Recommended primary servers

- **OpticStudio → `MCP_VV`.** It is the only one of the three that has been *built, tested and run*
  against the actually-installed OpticStudio 2026 R1.02. Its properties matter for a research
  pipeline specifically:
  - **Single universal reply envelope** (`ok`, `operation`, `elapsedMs`, `data`, `fault`, `notes`,
    `cached`, `ticket`) enforced by a contract test → uniform machine-readable results.
  - **11-code fault taxonomy with remediation hints** → an agent can recover rather than stall.
  - **`--guard` read-only mode, swept by test across all 39 tools** → safe exploratory analysis with
    no risk of mutating a validated design.
  - **JSON-lines audit journal** → *this is the provenance layer we need anyway* (doc 04 §2). Using
    `MCP_VV`'s journal rather than writing our own is a direct saving.
  - **`vv_analysis` escape valve** reaches any analysis the build supports by name → POP, Huygens
    PSF and the diffractive analyses are reachable without waiting for a wrapper.
  - **Revision-stamped read cache** → cached values can never disagree with the live design.

- **Lumerical → `pylumerical-mcp`.** Its value is the **26-topic, ~160 KB guidance corpus**, not its
  6 tools. For an agent driving FDTD, domain guidance (boundary conditions, mesh convergence, monitor
  placement, `grating*` usage) is worth more than tool count.

---

## 2. The gap: a SPEOS MCP server

No SPEOS MCP server exists. It is, however, **unusually easy to build here**, because two things are
already in place:

1. **A complete gRPC grammar is installed** (audit §2.3):
   `SPEOS_RPC\APIGrammar\ansys\api\speos\` containing `bsdf/` (`bsdf_creation.proto`,
   `anisotropic_bsdf.proto`, `spectral_bsdf.proto`), `sop/`, `vop/`, `source/`, `sensor/`,
   `simulation/`, `job/`, `scene/`, `part/`, `results/`, `lpf/`, `xmp/`, `intensity/`, `spectrum/`,
   `LTF/`, plus `grpc_stub.py`. The API surface is therefore *discoverable*, not reverse-engineered.
2. **A meta-prompt for exactly this task already exists**:
   `6_MCP_Comparison\prompt_lumerical.md` **§D — "Meta-prompt: retargeting this architecture to
   another product."**

### Proposed `ansys-speos-mcp`

| Tool | Purpose |
|---|---|
| `speos_connect` / `speos_info` | Attach to the SPEOS RPC server; report version |
| `speos_open` / `speos_save` | Project lifecycle |
| `speos_bsdf_create` | **Build a BSDF from arrays** — the Lumerical bridge (I4) |
| `speos_bsdf_validate` | Energy conservation, angular-bin convergence (gate G8) |
| `speos_source_define` | Sources incl. ray-file and intensity-distribution import |
| `speos_sensor_define` | Irradiance / intensity / radiance / colorimetric / human-vision sensors |
| `speos_simulate` | Direct / Inverse / Interactive; GPU selection |
| `speos_results` | `.xmp` maps, photometric and colorimetric summaries |
| `speos_lpf_query` | **Light-path forensics** — stray-light attribution (gate G9) |
| `speos_hud` | HOA / HIW plugin operations: virtual image, wedge angle, assembly tolerance |
| `speos_script` | Escape valve — arbitrary PySpeos against the live session |

**Design stance:** copy `MCP_VV`'s architecture, not its language — same universal reply envelope,
same fault taxonomy, same `--guard` mode, same audit journal, same "typed core + escape valve"
philosophy. Uniformity across the three servers is what makes agent orchestration tractable: one
result grammar, one error grammar, one safety model.

**Effort estimate:** 3–5 weeks. **Schedule:** P0/P1 (months 1–4), in parallel with the physics — it is
not on the critical path, and Stage 8 can proceed with direct PySpeos scripting if it slips.

---

## 3. Orchestration architecture

```
                       ORCHESTRATOR (LLM agent)
                    plans, dispatches, checks gates
                               |
        +----------------+-----+-----+----------------+
        |                |           |                |
   pylumerical-mcp    MCP_VV    ansys-speos-mcp   optislang (py)
        |                |           |                |
     Lumerical      OpticStudio    SPEOS          optiSLang
     FDTD/RCWA       ZOS-API       gRPC
        |                |           |                |
        +----------------+-----+-----+----------------+
                               |
                    PROVENANCE / ARTEFACT STORE
              HDF5 + JSON sidecars + MCP audit journals
                               |
                    VALIDATION HARNESS (gates G0-G11)
```

### Orchestrator–worker pattern

A pattern already worked through in this code base —
`6_MCP_Comparison\MCP_VV_Orchestrator_Worker_Example.md`.

- **Orchestrator** — owns the plan, the gates and the artefact store. Never touches a solver directly.
- **Workers** — one per tool, stateless between tasks, returning the universal envelope.
- **Gate checks** — pure functions over artefacts. Deterministic, re-runnable, no LLM judgement in the
  pass/fail decision.

> **Design rule:** *the LLM may decide **what** to run and **how** to adapt; it must never decide
> **whether a gate passed**.* Gate criteria are numeric and evaluated in code (doc 04). This keeps the
> agent's role squarely on the right side of the line for a scientific result.

---

## 4. Automation per stage

| Stage | Driver | Mechanism |
|---|---|---|
| 0 Provenance | Python | `common/provenance.py`; MCP journals |
| 1 Unit-cell library | `pylumerical-mcp` / `lumapi` | `addfdtd`, `addperiodic`, `addcircle`, `addsweep`, `grating*` |
| 2 LPA study | `lumapi` direct | Long jobs — scripted, queued, unattended overnight |
| 3 IFTA | Python | NumPy / SciPy |
| 4 Super-cell verify | `pylumerical-mcp` | `gratingorders` |
| 5 CGH | Python / PyTorch | CUDA |
| 6 Zemax sequential | **`MCP_VV`** | Typed tools + `vv_analysis` for POP / Huygens PSF |
| 7 Zemax NSC | **`MCP_VV`** | `vv_script` for NSC + dynamic-link configuration |
| 8 BSDF bridge | `ansys-speos-mcp` (or PySpeos) | `bsdf_creation.proto` |
| 9 SPEOS | `ansys-speos-mcp` | Simulate, sensors, `.lpf`, HOA/HIW |
| 10 optiSLang | Python | Integration nodes wrapping stages 1–9 |
| 11 Validation | Python | Gate harness |

---

## 5. Why this is a contribution in its own right

Three defensible claims, in increasing order of interest:

1. **Cross-vendor optical simulation is normally manual.** Nanophotonics, lens design and photometry
   live in different teams, different file formats and different mental models. An automated,
   provenance-tracked chain across all three is not standard practice.
2. **Agent-driven engineering simulation is largely unexplored in optics.** MCP is recent; almost all
   published agentic-science work is in chemistry, biology and materials, not optical engineering.
3. **The safety and provenance model is the interesting part.** `MCP_VV`'s guard mode, fault taxonomy
   and audit journal address the obvious objection — *"how do you trust a number an LLM produced?"* —
   with machine-checked answers rather than assurances. The honest response is that the agent
   *orchestrates* while *gates adjudicate*, and every artefact is reproducible from its sidecar.

**Suggested secondary output:** a short software/methods paper, or a section within the main article.
Candidate venues: *Optics Express* (software), *SoftwareX*, *Journal of Open Source Software*.

---

## 6. Reproducibility package

For M5 (doc 05 §7), to be released with a DOI:

```
holo-meta-ee/
  README.md                 quickstart, hardware and version requirements
  env/                      environment.yml, version manifest schema
  src/
    unitcell/               Lumerical sweep drivers
    lpa/                    finite-patch harness + correction model
    supercell/              IFTA over realisable phases
    cgh/                    PyTorch engine (forward model + SGD)
    zemax/                  ZOS-API / MCP_VV drivers
    speos/                  PySpeos drivers, BSDF builder
    validation/             gate harness G0-G11
    common/                 provenance, units, io
  configs/                  every run's full configuration
  data/
    libraries/              phase libraries (HDF5)
    lpa/                    LPA error maps
    results/                final result set
  figures/                  figure generation scripts
  LICENSE                   MIT
```

**Deliberately excluded:** vendor files we cannot redistribute (the Ansys DLLs, material databases,
licensed sample files). The package ships *drivers and data*, plus a manifest naming exactly which
vendor components and versions are required. This is the standard, defensible position for
commercial-tool reproducibility, and it should be stated plainly in the paper.

---

## 7. Risks specific to automation

| Risk | Impact | Mitigation |
|---|---|---|
| Licence contention between three concurrently driven products | Stalled runs | Tested in **P0.2**; serialise stages; queue with retry |
| MCP server version drift vs. Ansys updates | Broken pipeline | Pin `v261`; capture build dates in manifest; CI smoke test |
| Agent silently producing a wrong-but-plausible result | **Corrupted science** | **Gates are numeric and evaluated in code, never by the LLM (§3)** |
| SPEOS MCP server not delivered | Stage 8–9 manual | Direct PySpeos scripting — *not on critical path* |
| Long unattended runs failing overnight | Lost days | Checkpointing; sidecar written on completion only; automatic retry |
| ZOS-API COM threading instability | Crashes | `MCP_VV`'s single dedicated apartment thread already designs this out |

> Next: `09_Risk_Register_and_Validation.md`.
