# DECISIONS 002 — UX-Refactoring Eltern-Ansicht

**Spec:** `01_ux-refactoring-eltern-ansicht_SPEC.md`

---

## Zweck dieses Dokuments

Jede nicht-triviale Entscheidung während der Implementierung wird hier festgehalten — damit beim Resume oder beim Code Review klar ist, **warum** etwas so gemacht wurde wie es ist.

**Format:**
```
## DECISION-NNN — Kurztitel
**Datum:** YYYY-MM-DD
**Task:** TASK-XX
**Optionen:** A / B / ...
**Entscheidung:** Option X
**Begründung:** ...
```

---

## Offene Entscheidungen (vor Implementierungs-Start zu klären)

### Offene Frage A — Löschen: Modal oder Undo-Toast? (TASK-08)

**Optionen:**
- **A: Confirmation Modal** — Klick → Modal erscheint → Bestätigen → löschen
- **B: Undo-Toast** — Klick → sofort löschen → Toast „Rückgängig" für 5s

**Empfehlung:** Option A (Modal) — einfacher zu implementieren in Vanilla JS ohne Timer-Logik. Undo-Toast erfordert temporäres Speichern des gelöschten Eintrags.

> Wenn der Agent hier selbst entscheidet: bitte Entscheidung unten als DECISION-001 eintragen.

---

### Offene Frage B — Übertrag-Kontext: Tooltip oder Expander? (TASK-07)

**Optionen:**
- **A: Tooltip (hover/click)** — Info-Icon ⓘ, beim Klick/Hover erscheint Erklärungstext
- **B: Ausklappbarer Text** — Klick auf Zeile → Text klappt aus

**Empfehlung:** Option A (Tooltip via Click) — auf Mobile gibt es kein Hover. Click-Tooltip ist sowohl Desktop als auch Mobile-freundlich.

> Wenn der Agent hier selbst entscheidet: bitte Entscheidung unten als DECISION-002 eintragen.

---

### Offene Frage C — Progress Bar: korrigieren oder entfernen? (TASK-06)

**Kontext:** Progress Bar zeigt aktuell falsche Werte (Ellen fast voll obwohl fast ausgeglichen). TASK-01 entfernt die Progress Bar aus der Haupt-Card.

**Optionen:**
- **A: Im Expander korrekte Progress Bar anzeigen** — „Ausgezahlt: 14,74 € von 15,00 €"
- **B: Progress Bar komplett entfernen** — reine Text-Info reicht

**Empfehlung:** Option B (entfernen) — Text-Info ist bereits durch TASK-01 vorhanden. Ein zweiter Progress Bar im Expander ist redundant.

> Wenn der Agent hier selbst entscheidet: bitte Entscheidung unten als DECISION-003 eintragen.

---

## Decision Log (wird während der Implementierung befüllt)

<!-- Neue Einträge OBEN einfügen (neueste zuerst) -->

---

## DECISION-001 — TASK-08: Undo-Toast statt Modal
**Datum:** 2026-05-22  
**Task:** TASK-08 (Löschen ohne Bestätigung)  
**Optionen:** A (Confirmation Modal) / B (Undo-Toast)  
**Entscheidung:** Option B — Undo-Toast (bereits im Code vorhanden)  
**Begründung:** `softDelete()`, `renderUndoToast()` und `undoDelete()` waren vollständig implementiert, inkl. 5-Sekunden-Timer und Firebase-Restore. Kein Code-Change nötig. Option A wäre redundant.

## DECISION-002 — TASK-07: Click-Toggle statt Hover-Tooltip
**Datum:** 2026-05-22  
**Task:** TASK-07 (Übertrag-Kontext)  
**Optionen:** A (Hover-Tooltip) / B (Click-Toggle Expander)  
**Entscheidung:** Option B — Click-Toggle  
**Begründung:** Mobile hat kein Hover. Click-Toggle funktioniert auf Desktop und Mobile gleich. Implementierung als State-Variable `showCarryExplainer` passt zum bestehenden Render-Muster der App.
