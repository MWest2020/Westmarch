# Tasks: add-test-gate

## 1. Hub (dit repo)

- [ ] 1.1 `docs/reference/test-gate.md`: per-type verify-templates (Python uv+pytest;
      infra validate/lint; shell shellcheck+yaml-lint) + checknaam-afspraak `verify`
- [ ] 1.2 `inventory/repos.json`: veld `verify_gate` (yes|no|n/a) op de import-spokes;
      beginwaarden uit de inventarisatie (wordsworth=yes, rest=no, habitat/homelab n.t.b.)

## 2. Uitrol naar de spokes (signaal-model, geen branch protection)

- [ ] 2.1 skill-forge: `verify.yml` (uv + pytest op `tests/`)
- [ ] 2.2 zettelkast: `verify.yml` (uv + pytest op `tests/`)
- [ ] 2.3 habitat: `verify.yml` (shellcheck op `*.sh` + yaml-lint op manifests)
- [ ] 2.4 homelab: `verify.yml` (terraform validate + ansible-lint + kubeconform,
      voor zover de betreffende mappen aanwezig zijn)
- [ ] 2.5 wordsworth: al klaar (`ci.yml` draait pytest) — alleen `verify_gate=yes`
      bevestigen; eventueel de checknaam naar `verify` harmoniseren (optioneel)

## 3. Afronding

- [ ] 3.1 `verify_gate` in de inventaris gelijk aan de werkelijkheid per spoke
- [ ] 3.2 DoD: per uitgerolde spoke een rode én groene verify aangetoond op een PR
