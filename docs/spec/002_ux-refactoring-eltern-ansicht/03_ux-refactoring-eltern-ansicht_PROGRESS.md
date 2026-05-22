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

### 2026-05-22 — Phase 3 implementiert ✅ — SPEC 002 VOLLSTÄNDIG ABGESCHLOSSEN

**TASK-06** (Progress Bar child-view) — ✅ erledigt
- `pct`-Berechnung korrigiert: `elternGesamt / vereinbart` statt `(elternGesamt + carryOver) / vereinbart`
- carryOver hat die Bar verzerrt (Ellen: fast voll obwohl fast ausgeglichen) — jetzt zeigt Bar nur den echten Ausgabestatus dieses Monats
- Labels: "Ausgezahlt: X von Y €" + "Noch: Z" / "✓ Fertig" / "Überzahlt: Z" statt "Verrechnet" / "Schulden → Folgemonat"

**TASK-10** (Mobile Jahresübersicht) — ✅ erledigt
- CSS: `.year-table` (Desktop-only) + `.year-cards` (Mobile-only) + `@media(max-width:600px)`
- JS: Tabellen-Loop baut jetzt parallel auch Mobile-Cards
- Mobile-Card je Monat: Monatsname, Vereinbart, Ausgezahlt, Auslagen, Saldo als Klartext "Kind bekommt noch: X" / "Kind schuldet: X" / "Ausgeglichen ✓"
- Gesamtzeile bleibt in der Desktop-Tabelle; Mobile zeigt individuelle Monatskarten

**JS-Syntax-Check:** `node --check` → kein Fehler

**SPEC 002 Status:** Alle 10 UX-Tasks (TASK-01 bis TASK-10) abgeschlossen.
- Phase 1 ✅ (TASK-03, 04, 07, 08, 09)
- Phase 2 ✅ (TASK-01, 02, 05)
- Phase 3 ✅ (TASK-06, 10)

---

### 2026-05-22 — Phase 2 implementiert ✅

**TASK-05** (Button-Hierarchie) — ✅ erledigt
- CSS: `.btn-primary` (blau gefüllt #007aff) und `.btn-secondary` (outline, blau) in `<style>` ergänzt
- "Bargeld ausgezahlt" → `className:"btn btn-primary"`, "Auslage verbuchen" → `className:"btn btn-secondary"`

**TASK-02** (Negativ-Saldi) — ✅ erledigt
- Home-Card: neue Logik `mainLabel`: "Noch auszuzahlen" / "Alles ausgezahlt ✓" / "Überzahlt (nächsten Monat weniger)"
- Kein nacktes Minuszeichen mehr im Haupt-Wert der Home-Card
- Jahresübersicht: Header "Stand" → "Saldo"; Legende unter Tabelle: "Grün = Guthaben · Rot = Schulden"

**TASK-01** (Kognitiver Overload) — ✅ erledigt
- `expandedCards: {}` zum State hinzugefügt
- Gesamte Home-Card-Schleife neu aufgebaut:
  - Header (klickbar → Detailansicht): Avatar, Name, Betrag/Monat
  - Primär-Info (immer sichtbar): ein prominenter Wert in Farbe
  - Toggle "▼ Details anzeigen / ▲ Details ausblenden" (stopPropagation → öffnet nicht Detailansicht)
  - Details-Bereich: Bargeld, Auslagen, Übertrag (mit klarer Richtungs-Beschriftung), Progress Bar
- Progress Bar bleibt – aber nur im Detail-Bereich, nicht mehr auf der Hauptcard

**JS-Syntax-Check:** `node --check` → kein Fehler

**Nächster Schritt:** Phase 3 — TASK-06 (Progress Bar im child-view), TASK-10 (Mobile Jahresübersicht)

---

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
