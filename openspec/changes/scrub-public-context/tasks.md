# Tasks: scrub-public-context

## 1. Site-pagina's (direct, in deze branch)

- [x] 1.1 `docs/index.md`: trajectlabels weg, koppeling van een publiek
      repo aan een privaat repo weg, private-sectie-alinea vervangen
      door één kale verwijzingszin.
- [x] 1.2 `docs/homelab/herstel.md`: private repo-namen en
      redactie-datum verwijderd; alleen de functionele uitleg blijft.
- [x] 1.3 Beheer-host-actie (buiten dit publieke repo): de private
      overzichtspagina + `mkdocs.private.yml`-nav leven in de gitignored
      overlay op de beheer-host — bewust NIET in dit publieke repo (zou de
      inhoud alsnog publiceren, req 2). De publieke `--strict` build is
      geverifieerd groen zónder die pagina; de index verwijst er neutraal
      naar. Onderhoud van de private pagina hoort bij de private build op de
      host (Mark), niet bij deze publieke change.

## 2. Repo-bestanden (scope-besluiten Mark, 2026-07-14)

- [x] 2.1 `inventory/repos.json` + `repos.md`: financieringstrajecten en
      gevoelige-pointer-notes uit de publieke notes; verplaatst naar de
      private overlay in de gitignored `openspec/private/`.
      Validatievelden (`handbook_import`, `sensitivity`) ongemoeid.
- [x] 2.2 BESLOTEN: private build-config verhuist naar de beheer-host.
      `mkdocs.private.yml` ge-untrackt + gitignored (lokale kopie blijft
      voor de private build); `scripts/gen_imports.py` slaat een afwezige
      private config over, getest met en zonder.
- [x] 2.3 BESLOTEN: scrubben. `openspec/archive/`, `prep/seeds/` en
      `openspec/project.md` ontdaan van trajectvermeldingen;
      repo-namen blijven staan (repo-info mag publiek).
- [x] 2.4 BESLOTEN: git-history laten staan. Geen rewrite, geen
      visibility-wijziging; het verbod geldt vanaf nu.

## 3. Sluitstuk

- [x] 3.1 Spec-delta gemerged in `openspec/specs/handbook-portal/` en change
      gearchiveerd (openspec archive).
- [x] 3.2 Achterhaald: er is geen apart Westmarch-spec-repo meer — Westmarch is
      op 2026-07-12 hernoemd tot `handbook` (dit repo). De spec-delta wordt via
      3.1 onderdeel van dít repo; niets externs om synchroon te houden.

> Verificatie bij het afmaken (2026-07-22): volledige publieke repo gescand op
> `private-only` repo-namen in gerenderde pagina's en op financierings-/
> trajectkoppelingen. Twee residuen gefixt (test-gate.md noemde `zettelkast`;
> archief-proposal had nog "extern traject (repos…)"). Publieke build groen.
