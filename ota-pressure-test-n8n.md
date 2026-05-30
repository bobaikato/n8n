## Pressure-testing Ota on n8n: monorepo readiness with real execution pressure

n8n is a high-signal pressure repo: large pnpm monorepo, multiple development workflows, packaged quickstart paths (`npx`, Docker), and heavy build/test surfaces.

### Before Ota hardening

- repo readiness truth was distributed across docs, scripts, and CI
- task intent existed, but execution proof depth was still selective
- matrix coverage did not fully dry-run every non-internal task lane

### What we pressure-tested

- full non-internal task inventory in `ota.yaml`
- dry-run coverage for every non-internal task
- real execution coverage for non-runtime tasks
- workflow coverage across `app`, `backend`, `frontend`, `ai`, `runtime`, `instant`, and `docker`
- native and packaged runtime proof lanes in CI

### Contract hardening applied

- added `metadata.ota.minimum_version: "1.6.17"`
- moved sensitive files to protected boundaries:
  - `pnpm-lock.yaml`
  - `.env.local.example`
- pinned `agent.bootstrap.ota` install commands to `v1.6.17`
- fixed cross-platform native prerequisite behavior by guarding platform checks
  so non-Windows hosts no longer fail the Windows prerequisite check during unrelated task previews

### Matrix hardening applied

- upgraded packaged Ota setup lane from `1.6.14` to `1.6.17`
- expanded workflow inspection in native lanes
- added a full native dry-run coverage step for all non-internal tasks on the full backend lane

### What execution pressure exposed

- `install` can fail transiently based on upstream network/package fetch conditions (repo/environment signal, not contract shape)
- `build`/`build:docker` can fail when nested Corepack/pnpm downloads are unreachable
- `lint` can terminate with exit `137` under local resource pressure on large monorepo surfaces
- `test` can fail downstream when prerequisite heavy lanes are unstable

These are exactly the differences between contract validity and runtime readiness under real load, and they are valuable pressure findings.

### Why this matters

n8n showed the practical maturity bar: not only “is the contract valid,” but “can every declared lane be planned and pressure-executed consistently enough for humans, CI, and agents to trust the same path.”

### Evidence

- Contract: `<N8N_OTA_YAML_LINK>`
- Matrix workflow: `<N8N_MATRIX_WORKFLOW_LINK>`
- Green/native + docker runs: `<N8N_RUN_LINK_1>`, `<N8N_RUN_LINK_2>`
