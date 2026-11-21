# Herald / Kuma PoC Harness

Deterministic Proof-of-Concept (PoC) promotion and telemetry harness implementing a lossless **`populate -> annotate -> export`** pipeline for **Darker Side of Passion (`#DS0P` / `ledante.com`)**.

The harness serves strictly as a structural staging, attribution, and quota-telemetry layer—preserving complete eligible context sets while deferring editorial transformations to downstream agentic workflows.

---

## Core Architecture

### 1. Deterministic State Machine (`PromoteFSM`)
Each precursor passes through a strict four-stage finite state machine:

```text
init ──> read ──> brick ──> strip ──> post ──> done
```

1. **`read`**: Parses precursor YAML safely without side effects.
2. **`brick`**: Assembles core and optional bricks (`id`, `schema`, `base`, `motif`, `target`, `body`, `media`, `link`, `control`, `tags`, `closer`, `wenwu`) while skipping empty/null fields.
3. **`strip`**: Strips `_`-prefixed internal "invisible ink" keys (`_gates`, `_mianzi_shim`, `_legacy_pipeline`, `_bricks`, `_coreHEX`, `_precursor`) unless explicitly whitelisted in `_exceptions`.
4. **`post`**: Projects `atomic` and `fancy` JSON payloads, attaches telemetry/precedence metadata, and archives processed precursors to `sa/depot/` (in live mode).

### 2. Lossless `Populate -> Annotate -> Export` Policy
- **Populate**: Retains the complete eligible brand context (`branding.yml`), cleaned bricks, and full read-only tool taxonomy (`taxonomy.db`) without destructive pruning (`pruned: []`).
- **Annotate**: Evaluates projected character weight against tier ceilings (`atomic`: `280` chars, `fancy`: `1000` chars) and records overages as non-destructive telemetry (`ERR_BODY_EXCEEDS_BUDGET`, `ERR_QUOTA_EXCEEDED_LOGGED`).
- **Export**: Emits downstream JSON variants with hard-locked safety gates:
  - `allow_template_to_publish: false`
  - `web_sense_eligible: false`

### 3. Post-Edit Precedence & XYZ Vector Telemetry
Payloads trace the intended downstream execution order and multi-axis occupancy:
- **Precedence Chain**: `edit` (universal requirement) $\rightarrow$ `fsr` (gated to `fancy` / illuminated tier) $\rightarrow$ `wenwu` (alignment & pressure policy) $\rightarrow$ `mianzi` (attribution & tier projection).
- **XYZ Vectors**:
  - **X (Mythos)**: Motif, target, body occupancy, and style anchor (`closer`) state.
  - **Y (Transport)**: Surface, stage, and tier projection (`atomic` vs. `fancy`).
  - **Z (Tooling)**: Read-only tool roster grouped across `P0`–`P3` tiers (`taxonomy.db`).

---

## Repository & Workspace Layout

```text
herald/
├── poc_harness.py          # Core PromoteFSM & CLI harness
├── poc_harness_2.py        # Manifest-integrated harness build
├── prefetch.py             # Manifest resolver & bank/ -> sa/ staging module
├── prefetch_manifest.yml   # Declarative batch state & per-run arguments
└── README.md

kuma/                       # Resolved via $KUMA_ROOT or /media/ran/mu/kuma
├── data/
│   └── taxonomy.db         # Read-only SQLite taxonomy (13 seeded tools, P0–P3)
├── sa/                     # Active staging area (expected empty between runs)
│   ├── bank/               # Default precursor hold / export target
│   └── depot/              # Archived precursors post-run
└── out_poc/                # Emitted JSON variants (*_atomic.json, *_fancy.json)
```

---

## Single Sources of Truth (Read-Only)

- **Brand SSoT (`branding.yml`)**: `/media/ran/mu/notebook_runtime/src/notebooks/000/branding.yml`
  - Supplies brand identity, voice guardrails (`avoid`), keywords, hashtags, and tier-specific CTAs (`atomic` $\rightarrow$ `social_media`, `fancy` $\rightarrow$ `youtube`).
- **Tool Taxonomy (`taxonomy.db`)**: `/media/ran/mu/kuma/data/taxonomy.db`
  - Queried strictly via SQLite read-only URI (`?mode=ro`) across `P0` (core engines), `P1` (execution environment), `P2` (sub-pipelines), and `P3` (notebook anchors).

---

## Usage

### Dry-Run Validation (Recommended)
Validates YAML precursors, resolves `taxonomy.db` and `branding.yml`, simulates staging/depot moves, and prints projected JSON payloads without touching disk:

```bash
python poc_harness.py --dry-run
```

### Budget Overrides
Override default tier budgets (`atomic: 280`, `fancy: 1000`) to inspect headroom telemetry:

```bash
python poc_harness.py --dry-run --atomic-budget 500 --fancy-budget 2000
```

### Manifest-Driven Execution (`prefetch`)
Configure `prefetch_manifest.yml` alongside the harness to define `global_state` (`dry_run`, `expect_empty_sa`, `default_export_dir`) and per-run arguments (`tiers`, `atomic_budget`, `fancy_budget`, `cta_routing`, `quota_policy`, `precedence`):

```bash
python poc_harness_2.py --dry-run
```

---

## Current Scope & Guardrails

- **No Destructive Pruning**: Low-level quota logic logs headroom deficits rather than truncating prose or dropping taxonomy/brand attributes.
- **No Live Publishing**: All publishing and web-sense flags remain locked to `false`.
- **Immutable SSoTs**: Neither `branding.yml` nor `taxonomy.db` is ever modified by the harness.
