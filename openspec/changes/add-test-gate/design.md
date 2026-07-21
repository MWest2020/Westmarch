# Design — test-gate

## Beslissingen

### Geen centrale test-runner (clevere valkuil)
De verleiding is één reusable `test.yml` bij de hub, zoals docs-gates. Maar het
docs-contract is uniform (dezelfde checker past op elk repo); tests zijn dat niet.
wordsworth's suite start Postgres + SeaweedFS + OpenSearch; homelab valideert
Terraform/Ansible/K8s; habitat is shell + manifests. Eén workflow die dat allemaal
dekt wordt een onleesbare if-boom met per-repo geheimen — het tegenovergestelde van
saai. Daarom: elke spoke bezit zijn eigen `verify`-workflow; de hub levert
*templates* en *telt de dekking*, maar voert niets centraal uit.

### Eén checknaam: `verify`
Alle spokes noemen de check `verify` (job + workflow), zodat "elke spoke heeft een
groene verify op PR's" één telbare, uniforme afspraak is, ongeacht wat er ónder
draait. Dat is de uniformiteit die wél houdbaar is: de naam, niet het commando.

### Lint/validate telt als test waar unit-tests niet passen
habitat (shell/yaml) en homelab (infra) hebben geen zinvolle unit-suite. Een
geforceerde lege pytest zou schijnzekerheid geven. Voor die repos is de verify:
shellcheck + yaml-lint (habitat), `terraform validate` + `ansible-lint` +
`kubeconform` (homelab). Dat bewaakt wat er te bewaken valt zonder te faken.

### `verify_gate` in de inventaris, naast `contract_applied`
Dekking wordt data, geen aanname: `yes` (check draait), `n/a` (geen zinvolle
verify mogelijk — expliciet besloten), `no` (nog te doen). Zo is "welke spokes
missen nog een test-gate" één query, net als bij het docs-contract.

## Wat deze change NIET doet
- Geen tests schrijven — alleen afdwingen dat de bestaande suite/lint als
  PR-check draait. Ontbrekende testdekking is een repo-eigen zorg.
- Geen branch protection (signaal-model, solo-repo's).
- Geen wijziging aan docs-gates.

## Verificatie (DoD)
Per uitgerolde spoke: een PR met falende test/lint → verify-check rood; met
groene → groen. `verify_gate` in de inventaris klopt met de werkelijkheid
(één check per `yes`-spoke, zichtbaar op een PR).
