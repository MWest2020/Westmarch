---
status: current
last_reviewed: 2026-07-22
---

# Test-gate (verify-check per spoke)

De testhelft van "niet mergen zonder groene tests én up-to-date docs"
(docs-helft: [docs-gates](docs-gates.md)). Elke import-spoke heeft een PR-check
die **`verify`** heet; wát die draait hangt van het repo-type af. Er is bewust
**geen centrale test-runner** — tests zijn niet uniform zoals het docs-contract
(de een heeft service-containers nodig, de ander valideert infra). De hub levert
templates en telt de dekking (`verify_gate` in `inventory/repos.json`); de spoke
bezit zijn eigen workflow.

Handhaving is signaal, geen blokkade: rode X, geen branch protection op de
solo-repo's (zie [docs-gates](docs-gates.md#aanhaken-caller-template)).

## Template — Python (uv + pytest)

Voor spokes met een `pyproject.toml` en `tests/` (skill-forge, zettelkast, wordsworth):

```yaml
name: verify
on:
  pull_request:
    types: [opened, synchronize, reopened]
jobs:
  verify:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: astral-sh/setup-uv@v5
      - run: uv sync --frozen
      - run: uv run pytest -q
```

Draait je suite service-containers (Postgres/OpenSearch/…), gebruik dan `services:`
zoals wordsworth in zijn bestaande `ci.yml` doet — dat telt al als de verify.

## Template — shell + manifests (habitat)

Geen unit-suite; valideer wat er is. `--severity=warning` laat bewuste
info-noise (bv. SC2016 bij envsubst-`$VAR`) door; envsubst-templates worden
niet als statische YAML geparset (die valideren server-side na rendering):

```yaml
name: verify
on:
  pull_request:
    types: [opened, synchronize, reopened]
jobs:
  verify:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: shellcheck
        run: |
          sudo apt-get update -qq && sudo apt-get install -y shellcheck >/dev/null
          find . -name '*.sh' -not -path './openspec/changes/archive/*' -print0 \
            | xargs -0 -r shellcheck --severity=warning
      - name: yaml-syntax
        run: |
          python3 - <<'PY'
          import glob, yaml, sys
          bad = 0
          for f in glob.glob('**/*.yml', recursive=True) + glob.glob('**/*.yaml', recursive=True):
              if '/archive/' in f or 'job-template' in f:   # envsubst-template, geen statische YAML
                  continue
              try:
                  list(yaml.safe_load_all(open(f)))
              except Exception as e:
                  print(f'YAML-FOUT {f}: {e}'); bad = 1
          sys.exit(bad)
          PY
```

## Template — infra (homelab)

Cheap en zonder credentials/`init` (een signaal-gate, geen deploy):

```yaml
name: verify
on:
  pull_request:
    types: [opened, synchronize, reopened]
jobs:
  verify:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: hashicorp/setup-terraform@v3
      - name: terraform fmt
        run: terraform fmt -check -recursive
      - name: yaml-syntax
        run: |
          python3 - <<'PY'
          import glob, yaml, sys
          bad = 0
          for f in glob.glob('**/*.yml', recursive=True) + glob.glob('**/*.yaml', recursive=True):
              try:
                  list(yaml.safe_load_all(open(f)))
              except Exception as e:
                  print(f'YAML-FOUT {f}: {e}'); bad = 1
          sys.exit(bad)
          PY
```

## Dekking

`inventory/repos.json` draagt per import-spoke `verify_gate`:

- `yes` — een `verify`-check draait op PR's;
- `n/a` — geen zinvolle verify mogelijk (expliciet besloten; geen nep-suite);
- `no` — nog te doen.
