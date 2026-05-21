# PROGRESS — 001 Taschengeld App

> ## Briefing für den Implementierungsagenten
>
> Das ist das lebende Gedächtnis des Projekts. Bei **jeder** Wiederaufnahme diese Datei
> von oben nach unten lesen *bevor* irgendetwas anderes getan wird, dann die Zustandsproben
> aller abgehakten Tasks in `TASKS.md` ausführen. Realität und Checkboxen
> abgleichen, bevor fortgefahren wird.
>
> Nach **jedem** abgeschlossenen Task einen neuen Eintrag unten anhängen mit:
> - ISO-Zeitstempel (Datum + Uhrzeit)
> - Task-ID aus `TASKS.md`
> - Status: `erledigt` | `teilweise` | `blockiert` | `zurückgerollt`
> - 1–3 Sätze Zusammenfassung
> - Commit-Hash aus `./git-sync.sh` falls ein Commit gelandet ist
>
> Pro Eintrag eine einzelne `## YYYY-MM-DD HH:MM — <Task-ID> — <Status>`-Überschrift verwenden.
> Neuester Eintrag kommt an den **Anfang** des Protokolls.

---

## Protokoll

## 2026-05-17 — ÜBERSETZUNG — erledigt

- Alle vier Spec-Dateien (SPEC.md, TASKS.md, PROGRESS.md, DECISIONS.md) auf Deutsch übersetzt.
- Code-Snippets, Variablennamen und technische Bezeichner unverändert gelassen.
- Kein Code geändert.

## 2026-05-17 09:05 — REVIEW-1 — offene Fragen mit Maxi geklärt

- D-OQ-1 → **D-007** (geklärt): Inaktive Monate zählen als Guthaben. SPEC §6.3
  umgeschrieben um Zielverhalten zu beschreiben; SPEC §9 A5 angepasst. Task B2 in
  TASKS.md von „entscheiden" auf „implementieren" hochgestuft, mit konkretem
  Verifikationsszenario.
- D-OQ-2 → **D-008** (geklärt): `Sparziel ziel = 0` ist absichtlich.
  Wird nicht behoben. B1 in TASKS.md geschlossen.
- D-OQ-5 → **D-009** (geklärt): Spec-Verzeichnis wird ins öffentliche Repo committet.

## 2026-05-16 20:30 — INIT — rückwärts aus bestehendem Code abgeleitet

- `SPEC.md`, `TASKS.md`, `PROGRESS.md`, `DECISIONS.md` aus vollständiger
  Lektüre von `code/index.html` (493 Zeilen) und der Projektkontextnotiz
  `../taschengeld-kontext.md` (eine Ebene höher, außerhalb des Repos) geschrieben.
- Baseline-App-Verhalten in `SPEC.md` §4–§9 erfasst. Noch keine Code-Änderungen.
- Offene Fragen in `DECISIONS.md` geparkt (D-OQ-1 … D-OQ-5) und inline
  in `SPEC.md` §13 / `TASKS.md` (markiert mit `⚠️ INPUT BENÖTIGT`).
- Nächstes: Maxi beantwortet offene Fragen, dann ist T1 (URL-basierte Ansichtstrennung)
  der natürliche erste Implementierungstask.

<!-- Neue Einträge ÜBER dieser Zeile einfügen. -->
