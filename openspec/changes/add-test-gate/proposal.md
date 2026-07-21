# Change: add-test-gate

## Why

`add-docs-gates` bewaakt de docs-helft van "niet mergen zonder groene tests én
up-to-date docs". De andere helft — een groene testsuite als PR-check — ontbreekt
op de meeste spokes. Inventarisatie 2026-07-21:

| Spoke | testsuite aanwezig | test-CI op PR | gat |
|---|---|---|---|
| wordsworth | `tests/` + pytest | **ja** (`ci.yml`, pytest met service-containers) | — |
| skill-forge | `tests/` + pytest | nee | pytest-CI toevoegen |
| zettelkast | `tests/` + pytest | nee (alleen content-cron) | pytest-CI toevoegen |
| habitat | geen pytest (shell/yaml) | nee | shellcheck + yaml-lint |
| homelab | geen pytest (infra) | nee | terraform/ansible/k8s validate-lint |

De kern: "tests" is niet uniform zoals het docs-contract dat is. Een Python-spoke
draait `pytest`; een infrarepo draait `terraform validate`/`ansible-lint`/
`kubeconform`; habitat is shell + manifests (shellcheck, yaml-lint, de bestaande
worker-image-build valideert al). Eén centrale test-runner voor allemaal bestaat
niet — de wordsworth-suite heeft Postgres + SeaweedFS + OpenSearch nodig.

## What changes

- **Geen centrale test-runner** (bewust — zie design). Elke spoke krijgt een
  eigen `verify`-check op PR's, met een commando dat bij het repo-type past.
- **Per-type templates** in `docs/reference/test-gate.md`: Python (uv + pytest),
  infra (validate/lint), shell/manifests (shellcheck + yaml-lint).
- **Inventarisveld** `verify_gate` (`yes|no|n/a`) in `inventory/repos.json`, zoals
  `contract_applied`, zodat de dekking telbaar is en `check_freshness`-achtig
  gerapporteerd kan worden.
- **Uitrol** van de ontbrekende checks naar skill-forge, zettelkast (pytest),
  habitat (shellcheck+yaml-lint) en homelab (infra-validate). wordsworth is al klaar.
- **Handhaving = signaal**, net als docs-gates: rode X, geen branch protection op
  de solo-repo's (besluit Mark 2026-07-21).

## Impact

- Elke spoke krijgt (max) één extra PR-check; puur additief.
- Geen wijziging aan docs-gates of de hub-build.
- Repos zonder zinvolle testsuite (habitat/homelab) krijgen een lichte
  lint/validate-check i.p.v. een geforceerde unit-testsuite — geen schijnzekerheid.
