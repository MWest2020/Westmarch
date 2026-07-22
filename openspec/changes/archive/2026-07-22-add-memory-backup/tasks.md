# Tasks: add-memory-backup

> Retroactief; alles uitgevoerd 2026-07-22 (zie design.md-verificatie).

## 1. Split + eerste backup

- [x] 1.1 Split-regel bepaald (besluit Mark): gevoelig → zettelkast,
      voorkeuren → dotfiles; beide `private-only`.
- [x] 1.2 Canonieke memory-map als lokaal git-repo (geen remote); waarschuwing
      `README.gitnote` tegen publiek pushen.
- [x] 1.3 Eerste kopie gepusht: `zettelkast/claude-memory/` (MEMORY.md,
      homelab-cluster-toegang.md, handbook-hub-patroon.md, geheugen-backup.md)
      en `dotfiles/claude-memory/` (push-beleid.md, werkwijze-autonoom-afmaken.md).

## 2. Sync-script

- [x] 2.1 `dotfiles:scripts/sync-claude-memory.sh`: idempotent, fail-closed
      (onbekend → zettelkast), PRIVATE-guard, tijdelijke clones + cleanup.
- [x] 2.2 Getest: idempotent ("al up-to-date"), echte push bij wijziging,
      guard passeert; `set -u`-bug in de local-declaratie gefixt.

## 3. Vastlegging

- [x] 3.1 `geheugen-backup.md` in het geheugen: split-regel + verwijzing naar
      het script (allowlist-instructie voor nieuwe voorkeuren-bestanden).
