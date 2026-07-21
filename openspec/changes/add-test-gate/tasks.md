# Tasks: add-test-gate

## 1. Hub (dit repo)

- [x] 1.1 `docs/reference/test-gate.md`: per-type verify-templates (Python uv+pytest;
      infra validate/lint; shell shellcheck+yaml-lint) + checknaam-afspraak `verify`
- [x] 1.2 `inventory/repos.json`: veld `verify_gate` (yes|no|n/a) op de import-spokes;
      beginwaarden uit de inventarisatie (wordsworth=yes, rest=no, habitat/homelab n.t.b.)

## 2. Uitrol naar de spokes (signaal-model, geen branch protection)

- [x] 2.1 skill-forge: `verify.yml` (uv + pytest op `tests/`)
- [x] 2.2 zettelkast: `verify.yml` (uv + pytest op `tests/`)
- [x] 2.3 habitat: `verify.yml` (shellcheck op `*.sh` + yaml-lint op manifests)
- [x] 2.4 homelab: `verify.yml` — gekozen: `terraform fmt -check -recursive` +
      yaml-syntax (cheap, geen init/credentials; ansible-lint/kubeconform bewust
      niet — te zwaar/flaky voor een signaal-gate). Lokaal groen tegen main.
- [x] 2.5 wordsworth: `verify_gate=yes` bevestigd. Checknaam `test` (niet `verify`)
      BEWUST behouden: zijn ci.yml draait een zware integratiesuite (Postgres +
      SeaweedFS + OpenSearch); die niet aanraken voor een cosmetische rename. De
      spec accepteert een bestaande, equivalente check onder een andere naam.

## 3. Afronding

- [x] 3.1 `verify_gate` in de inventaris gelijk aan de werkelijkheid per spoke
- [x] 3.2 DoD: per uitgerolde spoke een rode én groene verify aangetoond op een PR
