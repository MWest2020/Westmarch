# Design — memory-backup

## Beslissingen

### Fail-closed classificatie (clevere valkuil)
De valkuil is een slimme inhoudsclassifier die per bestand "gevoelig?" raadt —
niet-deterministisch en dus onbetrouwbaar voor iets met lekrisico. Saai
alternatief: één expliciete allowlist van niet-gevoelige voorkeuren-bestanden
→ dotfiles; **al het overige → zettelkast**. Onbekend/nieuw geheugen belandt zo
automatisch in het meest-beschermde repo. De mens beslist bewust wat "veilig
genoeg voor dotfiles" is door een naam aan de allowlist toe te voegen.

### PRIVATE-guard vóór elke push
De grootste ramp is geheugen dat per ongeluk publiek wordt. Daarom checkt het
script `gh repo view … --json visibility` vóór elke push en breekt af als een
doel geen `PRIVATE` is — ook als een repo ooit per ongeluk publiek gezet wordt.
Defense-in-depth naast het feit dat de doelen nu privaat zijn.

### Kopieën, geen centrale store; canoniek blijft lokaal
De canonieke bron is en blijft `~/.claude/.../memory/` (lokaal git-repo, geen
remote) — dat is waar de recall uit leest. De repo's krijgen kopieën als
backup. Nadeel: drift tussen canoniek en kopie; daarom is de sync één commando
en idempotent (pusht alleen bij verschil).

### Script in dotfiles, niet in dit repo
Het is persoonlijke agent-tooling; dotfiles herbergt al de Claude Code-config
(`claude/`, `claude-memory/`). Dit repo (handbook) bezit alleen de *change* die
de afspraak vastlegt, niet de code.

## Wat deze change NIET doet
- Geen automatische trigger (cron/hook) — bewust handmatig draaien; een
  achtergrondsync die stil naar een repo pusht is te veel magie voor iets
  gevoeligs.
- Geen wijziging aan de recall (de harness leest de canonieke lokale map).

## Verificatie (uitgevoerd 2026-07-22)
- Split live gepusht: zettelkast/claude-memory (4 bestanden incl. index),
  dotfiles/claude-memory (2 voorkeuren-bestanden); beide `PRIVATE` bevestigd.
- Script idempotent getest: "al up-to-date" zonder wijziging; echte push bij
  een gewijzigde `geheugen-backup.md` → zettelkast `9876fab`, geverifieerd op
  `main`. PRIVATE-guard passeert; één `set -u`-bug (local-declaratie) gefixt.
