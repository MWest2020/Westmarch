# Change: scrub-public-context

## Why

Review van de live publieke homepage (2026-07-14, Mark): repo-info mag
publiek, maar subsidie-/financieringstrajecten en een opsomming van de
private sectie horen daar niet. De repo-splitsing (fail closed) dekt
alleen imports; hub-eigen pagina's én repo-bestanden (inventaris, configs,
seeds, archief) vallen daarbuiten — en dit repo is zelf publiek, dus alles
erin is publicatie.

## What changes

- Twee nieuwe requirements op `handbook-portal` (zie spec-delta):
  inhoudsverbod voor hub-eigen pagina's in de publieke build, en
  hetzelfde verbod voor niet-gerenderde repo-bestanden.
- Directe herstelactie op de site-pagina's (`docs/index.md`,
  `docs/homelab/herstel.md`): trajectlabels, private repo-namen en
  redactie-metadata verwijderd; private sectie teruggebracht tot één
  kale verwijzingszin.
- Volledige context (trajecten per repo, private-sectie-overzicht)
  verhuist naar een pagina die alleen in `mkdocs.private.yml`
  genavigeerd wordt.
- Vervolgtaken (scope-besluit Mark): inventaris-notes scrubben met
  private overlay, private import-config uit het publieke repo, archief/
  seeds scrubben, en het besluit over git-history (oude index blijft in
  de historie staan tot rewrite of visibility-wijziging).

## Impact

- Publieke Pages-deploy draait opnieuw bij merge naar main; de oude index
  verdwijnt van de site maar blijft in git-history tot daarover besloten is.
- Geen wijziging aan importlijst of validatielogica.
