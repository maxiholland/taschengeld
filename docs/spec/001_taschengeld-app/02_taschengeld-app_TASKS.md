# TASKS — 001 Taschengeld App

> ## Briefing für den Implementierungsagenten
>
> Diese Datei von oben nach unten lesen, bevor begonnen wird. Drei Regeln:
>
> 1. **Jeder Task hat einen Verifikationsbefehl.** Diesen ausführen, bevor die Checkbox gesetzt wird.
>    Eine Checkbox bedeutet „Ich habe die Verifikation ausgeführt und sie hat bestanden", nicht „Ich glaube, ich bin fertig".
> 2. **Bei Wiederaufnahme** den Verifikationsbefehl jedes bereits abgehakten Tasks ausführen.
>    Schlägt einer fehl, Checkbox entfernen, einen Eintrag in `PROGRESS.md` schreiben mit Erklärung,
>    und von dort fortfahren.
> 3. **Nach jedem Task** einen Eintrag in `PROGRESS.md` anhängen mit Zeitstempel,
>    Task-ID, Status und (falls zutreffend) dem Commit-Hash aus `./git-sync.sh`.
>
> `CLAUDE.md` lesen für Git/COMMENTS.md-Regeln — keine Git-Befehle direkt ausführen.

---

## Status

- **Spec-Status:** Rückwärts abgeleitet. Bestehende App vollständig in `SPEC.md` beschrieben.
- **Implementierungsstatus:** Baseline vollständig. Roadmap-Punkte unten sind
  die offene Arbeit.

---

## Taskliste

### Backlog — Priorität 1

- [ ] **T1 — URL-basiertes Routing (überarbeitet)**
  - Ziel: Drei URL-Modi gemäß SPEC.md §4.
    - `/` → Root: Kindnamen als Tipp-Buttons, kein 🔒-Button.
    - `/?kind={id}` → Kind-PIN-Flow → Einzelkind-Ansicht.
    - `/?eltern` → Eltern-PIN-Flow → Elternansicht.
  - Dateien: `code/index.html` — `window.location.search` beim Laden auswerten,
    `S.route` als neues State-Feld (`"root" | "kind" | "eltern"`), `S.activeKindId`.
  - Implementierungshinweise:
    - `renderRoot()`: lädt Firebase-Daten, zeigt nur `c.name`-Buttons. Tipp → `history.pushState` zu `/?kind={id}`.
    - `renderKindPin()`: prüft `localStorage["tg-kid-pin-{id}"]`, zeigt Set- oder Enter-Modal.
    - `renderKind()`: zeigt nur `S.data.children.find(c => c.id === S.activeKindId)`.
    - `renderEltern()`: unverändert, aber 🔒-Button entfernt.
    - „Abmelden" in Kindansicht: Session-Flag löschen, `history.pushState` zu `/`.
    - Unbekannte `?kind=`-ID: Hinweistext + Link zu `/`.
  - **Hinweis:** T15 (QR-Code) und T16 (Kind-PIN) bauen auf diesem Task auf — T1 zuerst fertigstellen.
  - Verifikation: T19–T25 in SPEC.md §11.

### Backlog — Priorität 1 (neu, aus UX-Review Mai 2026)

- [ ] **T15 — QR-Code-Sharing in Eltern-Detailansicht**
  - Ziel: Am Ende der Eltern-Detailansicht ein „Kind-Link teilen"-Bereich mit QR-Code und Kopier-Button.
  - Abhängigkeit: T1 muss fertig sein (`/?kind={id}`-URL-Schema muss existieren).
  - Bibliothek: `qrcode.js` via CDN (`https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js`). Kein Build-Schritt, kein npm.
  - Dateien: `code/index.html` — `renderElternDetail`, QR-Code-`<div>`, `new QRCode(el, url)`.
  - Implementierungshinweis: URL = `window.location.origin + window.location.pathname + "?kind=" + c.id`. Kopier-Button via `navigator.clipboard.writeText(url)`.
  - Verifikation: T25 in SPEC.md §11.

- [ ] **T16 — Kind-PIN (selbst gesetzt, localStorage, D-018)**
  - Ziel: `/?kind={id}` zeigt PIN-Set- oder PIN-Enter-Modal. Kind legt PIN selbst fest, kann ihn via „PIN vergessen" zurücksetzen. „PIN ändern" in der Kindansicht.
  - Abhängigkeit: T1 muss fertig sein.
  - Dateien: `code/index.html` — `renderKindPin()`, `renderKindPinSet()`, `renderKindPinEnter()`. localStorage-Keys: `tg-kid-pin-{id}`.
  - Implementierungshinweis: PIN-Flow analog zu bestehendem Eltern-PIN-Flow. „PIN vergessen" löscht `localStorage["tg-kid-pin-{id}"]` und wechselt in Set-Modus — kein weiterer Bestätigungsschritt nötig (URL = Identitätsbeweis, D-018). „PIN ändern" in Kindansicht: alten PIN bestätigen → neuen setzen.
  - Verifikation: T20–T23 in SPEC.md §11.

- [ ] **T8 — Wöchentlicher Zahlungsintervall**
  - Ziel: Beim Anlegen oder Bearbeiten eines Kindes kann zwischen „Monatlich" und „Wöchentlich" gewählt werden. Der vereinbarte Betrag wird pro Intervall gespeichert. In der Ansicht erscheint „X,XX € / Woche" bzw. „X,XX € / Monat". Die Berechnung `vereinbartProMonat = betrag * 4` (D-010).
  - Schemaänderung: `intervall: "monatlich" | "wöchentlich"` am Kind-Objekt. Feld `monatlBetrag` wird zu `vereinbartBetrag` (Rückwärts-Fallback nötig — beide Felder beim Lesen prüfen).
  - Dateien: `code/index.html` — Kind-Anlegen-Modal, Kind-Bearbeiten-Modal, `calcStats`, `getCarryOver`, alle Stellen wo `monatlBetrag` direkt gelesen wird.
  - Verifikation:
    - Kind mit 5 €/Woche anlegen. Monatliche Übersicht zeigt 20 € als „Vereinbart".
    - Bestehendes Kind (monatlBetrag ohne intervall) öffnen → zeigt Monatlich als Standard.
    - Wöchentliches Kind: 20 € `taschengeld` buchen → „Noch zu bekommen" = 0 €.

- [ ] **T9 — Undo-Toast statt kein Bestätigungsdialog**
  - Ziel: Jede Löschaktion (Eltern: taschengeld/auslage; Kind: eigene ausgabe) zeigt nach dem Löschen einen Toast „Transaktion gelöscht · Rückgängig" für 5 Sekunden. Tipp auf „Rückgängig" stellt die Transaktion wieder her.
  - Dateien: `code/index.html` — neuer `S.undoStack`-Eintrag (oder `S.lastDeleted`), neues `renderUndoToast()`-Element, Timeout-Logik.
  - Implementierungshinweis: `S.lastDeleted = { tx, childId, timeout }`. Bei neuem Löschen: alten Timeout clearen (vorherige Löschung wird final), neuen setzen.
  - Verifikation: T7 und T8 in der Smoke-Checkliste (SPEC.md §11).

- [ ] **T10 — Kind kann eigene Ausgaben löschen**
  - Ziel: In der Kindansicht zeigt jede `ausgabe`-Zeile des aktuell betrachteten Kindes einen `[×]`-Button. Löschen inklusive Undo-Toast (T9 muss zuerst fertig sein).
  - Dateien: `code/index.html` — `renderKind`, Bereich „Meine Ausgaben".
  - Wichtig: `[×]` nur bei Zeilen des jeweils eigenen Kindes rendern, falls mehrere Kinder in der Kindansicht sichtbar sind. Derzeit sehen alle Kinder alle Geschwister (D-014) — daher `[×]` nur bei `c.id === aktivesKindId` oder: `[×]` bei allen sichtbaren Kindern für ihre jeweils eigenen ausgaben (kein Kind kann Ausgaben anderer Kinder löschen, da es keine Kindidentifikation gibt — Entscheidung nötig, ob Kind sich selbst auswählt oder alle Ausgaben löschbar sind).
  - Kindidentität ist gelöst: `S.activeKindId` aus URL `?kind={id}` (T1/T16). `[×]` nur bei ausgabe-Zeilen des Kindes mit `c.id === S.activeKindId`.
  - Abhängigkeit: T1 und T16 müssen fertig sein.
  - Verifikation: T11 in SPEC.md §11.

- [ ] **T11 — Freie Betragseingabe bei Sparzielen**
  - Ziel: Neben den `+1€ / +2€ / +5€`-Buttons erscheint ein Freitextfeld `[____€ ✓]`. Eingabe akzeptiert Dezimalbeträge (`3,50` → `3.50`). Betrag wird auf `ziel - gespart` begrenzt. Nach Bestätigung wird das Feld geleert.
  - Dateien: `code/index.html` — `renderSparzielButtons`, neues Input-Element + Confirm-Button.
  - Verifikation: T13 in SPEC.md §11.

- [ ] **T12 — Kind umbenennen**
  - Ziel: In der Eltern-Detailansicht zeigt der Kindname ein `✎`-Icon. Tipp öffnet Inline-Textfeld. Bestätigung via Enter oder ✓. Leeres Feld → keine Aktion. Escape oder Tipp außerhalb → abbrechen.
  - Dateien: `code/index.html` — `renderElternDetail`, neuer Edit-State `S.editingChildName`.
  - Verifikation: T14 in SPEC.md §11.

- [ ] **T13 — Empty-State / Onboarding**
  - Ziel: Wenn `data.children` leer oder null ist, zeigen beide Ansichten einen sinnvollen Leerstand statt einer leeren Liste. Elternansicht: CTA „+ Erstes Kind hinzufügen". Kindansicht: freundlicher Hinweistext, kein 🔒-Button.
  - Dateien: `code/index.html` — `renderEltern`, `renderKind`.
  - Verifikation: T15 und T16 in SPEC.md §11.

### Backlog — Priorität 2 (noch nicht verfeinert)

- [ ] **T2 — Multi-Kind-Übersicht auf Eltern-Startseite**
  - Ziel: Eltern-Startseite zeigt Seite-an-Seite-Vergleich der Schlüsselstatistiken
    aller Kinder zusätzlich zu den bestehenden Kindkarten.
  - Benötigt Design-Pass vor Implementierung.
  - Verifikation: ⚠️ INPUT BENÖTIGT — was genau soll verglichen werden?

- [ ] **T3 — Automatische monatliche Taschengeld-Gutschrift**
  - Ziel: Am 1. jedes Monats automatisch eine `taschengeld`-Transaktion
    für jedes Kind in Höhe von `monatlBetrag` buchen.
  - Offene Frage: Soll dies bei jedem Seitenaufruf eines neuen Monats auslösen,
    oder via echtem Cron? (Kein Server, daher ist ein clientseitiger Trigger beim
    Laden die einzige Option.)
  - Verifikation: ⚠️ INPUT BENÖTIGT — definieren wann Auto-Gutschrift „fällig" ist und
    wie Duplikatverhinderung funktioniert (wahrscheinlich ein Idempotenz-Schlüssel
    pro `${childId}-${monatKey}-auto`).

- [ ] **T4 — Browser-Benachrichtigung „Taschengeld liegt bereit"**
  - Ziel: Das Kind (ihr Gerät) benachrichtigen wenn eine elternseitige `taschengeld`-
    Transaktion gerade gebucht wurde.
  - Benötigt: Notification-API-Berechtigungsflow, Polling oder onSnapshot von
    Firebase, Deduplizierungslogik. ⚠️ INPUT BENÖTIGT.

- [ ] **T5 — Ausgaben-Kategorien**
  - Ziel: Optionales `kategorie`-Feld bei `ausgabe`-Transaktionen, plus
    Zusammenfassungsbereich pro Kategorie.
  - Schemaänderung: `kategorie?: string` zur Transaktion hinzufügen. Rückwärtskompatibel
    (ältere Transaktionen bleiben ohne Kategorie).
  - Verifikation: Ausgabe mit „Süßes" hinzufügen, nach Kategorie filtern, Gesamtsumme sehen.

- [ ] **T6 — PDF/CSV-Export**
  - Ziel: Eltern können eine Monatsabrechnung eines Kindes als PDF oder CSV herunterladen.
  - Format ⚠️ INPUT BENÖTIGT (PDF-Layout, CSV-Spalten).

- [ ] **T14 — Jahresübersicht**
  - Ziel: Eltern können eine Jahresansicht pro Kind aufrufen, die alle 12 Monate tabellarisch zeigt: Monat, Vereinbart, Ausgezahlt, Auslagen, Offen/Überschuss. Gesamtzeile am Ende.
  - Einstieg: z. B. ein „Jahresübersicht"-Button in der Eltern-Detailansicht.
  - ⚠️ INPUT BENÖTIGT: Soll die Ansicht alle Kinder oder nur das ausgewählte zeigen? Soll sie druckbar/exportierbar sein?

### Backlog — Bugs / Schulden aus §13 in SPEC.md

- [x] **B1 — `Sparziel` mit `ziel = 0`** — *Wird nicht behoben per D-008.*
  Verhalten ist absichtlich. Ohne Codeänderung geschlossen.

- [ ] **B2 — Übertrag muss inaktive Monate als Guthaben zählen**
  - Gemäß D-007 / SPEC §6.3 soll `getCarryOver` jeden Monat
    vom Erstellungsdatum des Kindes bis `beforeMk` iterieren, nicht nur Monate mit
    Transaktionen.
  - Dateien: `code/index.html`, Funktion `getCarryOver`.
  - Implementierungshinweis:
    - Den ersten Monat aus `c.id` ableiten (das ist ein `Date.now()`-String):
      `new Date(parseInt(c.id))` → `"YYYY-MM"`.
    - Den Monatsbereich von `firstMk` bis `beforeMk` (exklusiv)
      mit dem bestehenden `stepM`-Helper aufbauen.
    - Für jedes `mk` im Bereich: `debt += elternGesamt(mk) - vereinbart`.
  - Verifikation (manuell):
    - Ein neues Kind im aktuellen Monat mit `monatlBetrag = 10` erstellen.
    - Einen Monat zurückgehen, dann noch einen — angezeigter `carryOver` sollte
      `0` sein für den Erstellungsmonat und die Monate **vor** der Erstellung
      (die sind außerhalb des Bereichs).
    - Im aktuellen Monat: `carryOver` sollte `0` sein (keine Vormonate zum Gutschreiben).
    - Einen Monat nach Erstellung vorwärts gehen: keine Transaktion buchen. Noch einen
      Monat vorwärts: `carryOver` sollte `−10 €` sein (Kind hat 10 € aus dem inaktiven
      Monat gut).
    - `taschengeld 10 €` in jenem inaktiven Monat buchen: `carryOver` sinkt
      nächsten Monat zurück auf `0`.
  - Zustandsprobe: identisch mit Verifikation — Szenario in einem frischen
    Test-Kind nachbauen und Zahlen bestätigen.

### Optional — Test-Infrastruktur (separate Spec)

- [ ] **T7 — Reine Logik in testbares Modul extrahieren**
  - Ziel: `getCarryOver` und `calcStats` in eine separate Datei ohne
    DOM-Abhängigkeiten auslagern, damit sie unit-getestet werden können.
  - Erfordert: Einführung eines minimalen Test-Runners (Vitest im Browser oder
    ein `<script type="module">`-Setup). Braucht eigene Spec.

---

## Definition of Done — gilt für jeden Task

- Alle Abnahmekriterien in `SPEC.md` §9 bestehen via §11 manuelle Checkliste.
- Verifikationsbefehl oben beim Task besteht.
- `PROGRESS.md` aktualisiert mit Task-ID, Status, Zeitstempel, Commit-Hash.
- `COMMENTS.md` hat einen `[PENDING]`-Block, der die Änderung beschreibt.
- Maxi hat `./git-sync.sh` ausgeführt und die bereitgestellte Seite funktioniert.
