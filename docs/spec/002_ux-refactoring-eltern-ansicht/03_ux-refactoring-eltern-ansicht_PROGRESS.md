# PROGRESS 002 — UX-Refactoring Eltern-Ansicht

**Spec:** `01_ux-refactoring-eltern-ansicht_SPEC.md`  
**Tasks:** `02_ux-refactoring-eltern-ansicht_TASKS.md`

---

## ⚠️ Anleitung für den Implementations-Agenten

Dieses Dokument ist dein **Gedächtnis** zwischen Sessions. Führe es konsequent.

**Beim Start jeder Session:**
1. Diese Datei zuerst lesen — verstehe, wo die letzte Session aufgehört hat
2. Tasks-Datei lesen — prüfe abgehakte Checkboxen mit Verifikations-Schritten
3. Wenn ein Verifikations-Schritt fehlschlägt → Checkbox zurücksetzen + Task neu bearbeiten

**Nach jeder erledigten Task:**
1. Kurzen Eintrag hier hinzufügen (Timestamp, Task-ID, Status, was gemacht wurde)
2. Eintrag in `COMMENTS.md` hinzufügen (Format: `## [PENDING] YYYY-MM-DD HH:MM`)
3. Checkbox in `02_ux-refactoring-eltern-ansicht_TASKS.md` setzen

**Format für Einträge:**
```
## YYYY-MM-DD HH:MM — TASK-XX [completed|blocked|in-progress]
- Was wurde gemacht
- Welche Zeilen/Funktionen in index.html geändert
- Besondere Beobachtungen / Überraschungen
```

---

## Session-Log

### 2026-05-22 — Phase 1 implementiert ✅

**TASK-08** (Löschen ohne Confirmation) — **Bereits implementiert, kein Code-Change**
- `softDelete()`, `undoDelete()`, `renderUndoToast()` sind vollständig im Code vorhanden
- Undo-Toast erscheint 5 Sekunden nach dem Löschen mit "Rückgängig"-Button
- Entscheidung: Option B (Undo-Toast) war bereits gewählt — dokumentiert in DECISIONS.md

**TASK-03** (Zeiteinheit bei "Vereinbart") — ✅ erledigt
- `index.html`: stat-val zeigt jetzt `fmt(rawBetrag)` statt `fmt(s.vereinbart)`
- Subtext "pro Monat" / "pro Woche" direkt darunter (11px, grau)
- Gilt nur für child-view Detailansicht; home-view hatte Einheit bereits korrekt

**TASK-04** (Rote Null-Euro-Anzeige) — ✅ erledigt
- `index.html`: Variable `ausgebenColor` eingeführt
- 0 € → `#aaa` (Grau), ausgaben > vereinbart → `#d4537e` (Rot), sonst → `#1d1d1f` (Neutral)

**TASK-07** (Übertrag-Kontext) — ✅ erledigt
- `index.html`: `showCarryExplainer: false` zum State hinzugefügt
- carry-box komplett neu: klickbares ⓘ-Icon, toggle öffnet Klartext-Erklärung
- Unterscheidet Überzahlung (zu viel bekommen) vs. Ausstehend (zu wenig bekommen)

**TASK-09** (QR-Code-Erklärung) — ✅ erledigt
- `index.html`: Titel geändert zu "Kind-App-Zugang für [Name]"
- 3 Bullet-Punkte: ✓ sehen, ✓ eintragen, ✗ ändern/löschen

**JS-Syntax-Check:** `node --check` → kein Fehler

**Nächster Schritt:** Phase 2 — TASK-02 (Negativ-Saldi), TASK-01 (Card-Overload), TASK-05 (Button-Hierarchie)

---

### 2026-05-22 — Spec erstellt

- Spec `002_ux-refactoring-eltern-ansicht` angelegt auf Basis von `docs/UX_AUDIT_Taschengeld_App.md`
- 10 UX-Probleme als Tasks erfasst, phasiert in Phase 1 / 2 / 3
- Wireframes, Akzeptanzkriterien, Tests, Mermaid-Flows dokumentiert
- **Noch keine Code-Änderungen** — Spec ist bereit zur Übergabe an Implementations-Agent

**Nächster Schritt:** TASK-03 (Zeiteinheit bei „Vereinbart") als erster Quick Win

---

<!-- Neue Einträge OBEN einfügen (neueste zuerst) -->
