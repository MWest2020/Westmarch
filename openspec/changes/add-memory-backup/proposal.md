# Change: add-memory-backup

> Retroactief vastgelegd op 2026-07-22: de opzet bestond al (besluit Mark in
> dezelfde sessie) vóórdat deze change werd geschreven. Vastgelegd voor het
> spoor, niet herontworpen.

## Why

Het geheugen van de coördinerende Claude Code-sessie
(`~/.claude/projects/-home-agent-habitat/memory/`) was ongversiebeheerd en
alleen lokaal aanwezig — één host-crash en het is weg. Tegelijk is de inhoud
**gevoelig**: cluster-toegang, orchestrator-host, age-key-locatie en private
repo-/homelab-identifiers. Het mag dus nooit naar een publiek repo, maar wél
ergens duurzaam en versiebeheerd staan. Er was geen mechanisme dat dat veilig
regelde.

## What changes

- **Split-regel op gevoeligheid** (private repo's, beide `sensitivity:
  private-only`):
  - gevoelige homelab-/topologie-memory + de index → `MWest2020/zettelkast`
    (`claude-memory/`);
  - werkwijze-/voorkeuren-memory → `MWest2020/dotfiles` (`claude-memory/`).
- **`dotfiles:scripts/sync-claude-memory.sh`**: idempotent sync-script dat het
  canonieke geheugen naar die repo's pusht, **fail-closed** (onbekende
  bestanden → zettelkast) met een **PRIVATE-guard** (weigert push naar een
  niet-`PRIVATE` repo).
- **Canonieke bron blijft lokaal**: de memory-map is een lokaal git-repo zonder
  remote; de repo-kopieën zijn de versiebeheerde backup.
- **`geheugen-backup.md`** in het geheugen zelf legt de split-regel en het
  script vast, zodat een volgende sessie het weet.

## Impact

- Geheugen overleeft een host-verlies en heeft historie, zonder lekrisico.
- Geen wijziging aan de publieke handbook-build, inventaris of gates.
- Onderhoud: na een geheugenwijziging het script draaien; een nieuw
  voorkeuren-bestand toevoegen aan de `DOTFILES_FILES`-allowlist, anders gaat
  het (veilig) naar zettelkast.
