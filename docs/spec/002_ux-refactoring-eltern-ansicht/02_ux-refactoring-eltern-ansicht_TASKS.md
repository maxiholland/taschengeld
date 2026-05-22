# TASKS 002 — UX-Refactoring Eltern-Ansicht

**Spec:** `01_ux-refactoring-eltern-ansicht_SPEC.md`  
**Progress-Journal:** `03_ux-refactoring-eltern-ansicht_PROGRESS.md`  
**Decision Log:** `04_ux-refactoring-eltern-ansicht_DECISIONS.md`

---

## ⚠️ Briefing für den Implementations-Agenten

**Vor dem Start lesen:**

1. Lies zuerst `01_ux-refactoring-eltern-ansicht_SPEC.md` vollständig.
2. Die App ist eine **Single-File-Webapp** — alle Änderungen gehen in `code/index.html`.
3. Nach **jeder abgeschlossenen Task** schreibst du einen Eintrag in `COMMENTS.md` (Format: `## [PENDING] YYYY-MM-DD HH:MM`).
4. Nach **jeder abgeschlossenen Task** schreibst du einen Eintrag in `03_ux-refactoring-eltern-ansicht_PROGRESS.md`.
5. **Kein Git** — `git add/commit/push` niemals ausführen. Maxi macht das mit `./git-sync.sh`.
6. Stopp-Bedingungen → siehe §14 der SPEC.

**Resume-Regel:** Beim Start einer neuen Session prüfe zuerst alle abgehakten Tasks mit ihren Verifikations-Schritten. Wenn ein Verifikations-Schritt fehlschlägt, setze die Checkbox zurück und bearbeite den Task erneut.

---

## Phase 1 — Quick Wins (geschätzter Aufwand: ~7h)

*Diese Phase zuerst — sie behebt die schlimmsten Verwirrungen ohne große Umbauten.*

---

### TASK-03 🔴 "Vereinbart" mit Zeiteinheit auf allen Screens
**Prio:** Hoch | **Aufwand:** ~1h | **Audit:** Fix #3

**Beschreibung:**  
In der Kind-Detailansicht fehlt die Zeiteinheit bei „Vereinbart". Auf allen Screens muss überall `15,00 € / Monat` stehen (oder äquivalentes Label).

**Konkret:**
- Suche alle Stellen in `index.html`, die `vereinbart` oder den Betrag ohne Zeiteinheit rendern
- Füge `/Monat` oder `/Woche` (je nach tatsächlicher Einheit in der DB) hinzu
- Alternativ: Label umbenennen zu „Monatliches Taschengeld: 15,00 €"

**Verifikation:**
- [x] Task erledigt
- Öffne App → Monatsübersicht → Kind-Detailansicht → Jahresübersicht
- Prüfe: Überall ist die Zeiteinheit sichtbar
- Browser-Test T-03 bestanden

---

### TASK-04 🔴 Rote Null-Euro-Anzeige entfernen
**Prio:** Hoch | **Aufwand:** ~1h | **Audit:** Fix #4

**Beschreibung:**  
`Kind ausgegeben: 0,00 €` wird aktuell in Rot angezeigt. Das ist irreführend. Farb-Logik ändern:
- `ausgegeben = 0` → Grau/Neutral
- `ausgegeben > vereinbart` → Rot (Überzug)
- `ausgegeben > 0 && ausgegeben <= vereinbart` → Grün oder Neutral

**Konkret:**
- Finde die CSS-Klasse oder Inline-Style, der den Betrag rot färbt
- Ändere die Bedingung: Rot nur wenn `ausgegeben > vereinbart`
- Teste den Fall `ausgegeben = 0`

**Verifikation:**
- [x] Task erledigt
- DevTools: `getComputedStyle(element).color` für „0,00 €"-Anzeige → nicht Rot
- Browser-Test T-04 bestanden

---

### TASK-07 🟡 Übertrag-Kontext mit Erklärung
**Prio:** Medium | **Aufwand:** ~2h | **Audit:** Fix #7

**Beschreibung:**  
„Übertrag aus Vormonat: -8,75 €" hat keinen Kontext. Ergänze:
- Info-Icon (ⓘ) mit Tooltip **oder** ausklappbaren Erklärungstext
- Text: „Kind hat im [Vormonat] 8,75 € zu viel bekommen. Wird vom aktuellen Taschengeld abgezogen."
- Gelber Hintergrund nur wenn Betrag negativ (Warnung), neutral wenn positiv

**Entscheidungs-Option:** Tooltip (einfacher) oder Expander (besser lesbar auf Mobile). Entscheide und dokumentiere in `04_ux-refactoring-eltern-ansicht_DECISIONS.md`.

**Verifikation:**
- [x] Task erledigt
- Kind mit negativem Übertrag aufrufen → Erklärung erscheint beim Klick/Hover auf ⓘ
- Browser-Test T-08 bestanden

---

### TASK-08 🟡 Löschen mit Bestätigung (Confirmation Modal oder Undo-Toast)
**Prio:** Medium | **Aufwand:** ~2h | **Audit:** Fix #8

**Beschreibung:**  
Transaktion-Löschen-Icon öffnet aktuell sofort ohne Bestätigung. Implementiere:
- **Option A:** Confirmation Modal mit „Transaktion wirklich löschen?" + [Abbrechen] / [Löschen]
- **Option B:** Undo-Toast nach Löschen (5 Sekunden Zeitfenster zum Rückgängig-Machen)

Wähle die technisch einfachere Option und dokumentiere in `04_ux-refactoring-eltern-ansicht_DECISIONS.md`.

**Konkret (Option A — Modal):**
```html
<div id="delete-confirm-modal" style="display:none">
  <div class="modal-backdrop">
    <div class="modal-box">
      <p>Transaktion <strong id="delete-modal-label"></strong> wirklich löschen?</p>
      <button id="delete-cancel">Abbrechen</button>
      <button id="delete-confirm">Löschen</button>
    </div>
  </div>
</div>
```

**Verifikation:**
- [x] Task erledigt
- Trash-Icon klicken → kein sofortiges Löschen → Dialog/Toast erscheint
- [Abbrechen] → Transaktion bleibt vorhanden
- [Löschen] → Transaktion verschwindet
- Browser-Test T-06 bestanden

---

### TASK-09 🟡 QR-Code mit erklärendem Text
**Prio:** Medium | **Aufwand:** ~1h | **Audit:** Fix #9

**Beschreibung:**  
Vor dem QR-Code steht aktuell nur „Kind-Link teilen" ohne Erklärung. Ergänze:
- Überschrift: „Kind-App-Zugang für [Kind-Name]"
- Erklärungstext: Was kann das Kind (Guthaben sehen, Ausgaben eintragen) und was nicht (löschen, bearbeiten)
- Optional: Link „> Was kann [Kind-Name] damit machen?" (Toggle-Text)

**Verifikation:**
- [x] Task erledigt
- Kind-Detailansicht → QR-Code-Bereich → Erklärungstext ohne Klick sichtbar
- Browser-Test T-09 bestanden

---

## Phase 2 — High-Impact-Fixes (geschätzter Aufwand: ~9h)

*Erst starten wenn Phase 1 vollständig abgeschlossen und committed (via git-sync.sh).*

---

### TASK-02 🔴 Negativ-Saldi klarstellen
**Prio:** Hoch | **Aufwand:** ~3h | **Audit:** Fix #2

**Beschreibung:**  
Alle negativen Beträge in der App müssen explizit beschriftet sein. Kein blankes „-8,75 €".

**Konkret:**
1. Jahresübersicht-Tabelle: Spalten-Header anpassen — Saldo-Spalte erhält Untertitel „(+ bekommt / – schuldet)"
2. Alle negativen Saldo-Beträge rendern als: „Kind schuldet: 9,01 €" (Rot) statt „-9,01 €"
3. Alle positiven Saldo-Beträge rendern als: „Noch zu zahlen: 8,00 €" (Grün) oder Betrag in Grün
4. Übertrag-Werte: Label „Überzahlt: 8,75 €" statt „-8,75 €"
5. Farb-Coding konsistent: Grün (bekommt Geld), Rot (schuldet), Grau (ausgeglichen)

**Verifikation:**
- [x] Task erledigt
- Suche in gerenderten DOM-Elementen nach `-[0-9]` — sollte kein bloßes Minuszeichen mehr geben
- Browser-Test T-02 bestanden

---

### TASK-01 🔴 Kognitiven Overload in der Kind-Card reduzieren
**Prio:** Hoch | **Aufwand:** ~4h | **Audit:** Fix #1

**Beschreibung:**  
Die Kind-Card zeigt 7 Datenfelder gleichzeitig. Ziel: maximal 3 sichtbar, Rest in Expander.

**Konkret:**
1. Kind-Card Primär-Info (immer sichtbar):
   - Name + Avatar
   - Monatsbetrag (kleiner Grau-Text): „15,00 € / Monat"
   - **Hauptinfo** (groß, farbig): „Noch auszuzahlen: 0,26 €" (grün) / „✓ Alles ausgezahlt" (grau) / „Überzahlt: 8,75 €" (rot)

2. Expander „Details anzeigen" / „Details ausblenden":
   - Bargeld-Zeilen mit Datum
   - Auslagen-Zeilen mit Datum
   - Übertrag mit Erklärung (aus TASK-07)
   - Berechnungsübersicht: „Vereinbart – Ausgezahlt + Übertrag = Noch offen"

3. Progress Bar entfernen (oder durch einfaches Icon ersetzen)

4. Berechnung `offen = vereinbart - ausgezahlt + übertrag` prüfen — muss korrekt sein

**Hinweis:** Das ist der größte Umbau. Vor dem Start den Maxi kurz bestätigen lassen, dass Phase 1 erfolgreich war.

**Verifikation:**
- [x] Task erledigt
- Kind-Card zählen: ≤3 Datenfelder ohne Expander
- Expander klicken → Details erscheinen
- Expander schließen → Details verschwinden
- Browser-Test T-01, T-07 bestanden

---

### TASK-05 🟡 Button-Hierarchie einführen
**Prio:** Medium | **Aufwand:** ~2h | **Audit:** Fix #5

**Beschreibung:**  
„Bargeld ausgezahlt" und „Auslage verbuchen" sehen identisch aus. Differenzierung:
- „Bargeld ausgezahlt" → Primary: gefüllter Button (Hintergrundfarbe = Primärfarbe der App)
- „Auslage verbuchen" → Secondary: outline-only, kein Hintergrund

**Konkret:**
```css
.btn-primary {
  background-color: var(--primary-color, #4a90e2);
  color: white;
  border: none;
}
.btn-secondary {
  background: none;
  border: 2px solid var(--primary-color, #4a90e2);
  color: var(--primary-color, #4a90e2);
}
```

**Verifikation:**
- [x] Task erledigt
- Kind-Detailansicht → 2 Buttons visuell different
- Browser-Test T-05 bestanden

---

## Phase 3 — Optimierungen (geschätzter Aufwand: ~7h)

*Erst starten wenn Phase 2 vollständig abgeschlossen.*

---

### TASK-06 🟡 Progress Bar ersetzen oder korrigieren
**Prio:** Medium | **Aufwand:** ~3h | **Audit:** Fix #6

**Beschreibung:**  
Progress Bar ist aktuell inkonsistent und verwirrend (Ellen: fast voll obwohl fast ausgeglichen; Adrian: leer obwohl 8€ offen).

**Option A: Korrekte Progress Bar** (bevorzugt wenn TASK-01 Expander bereits da):
- Entferne Progress Bar aus der Haupt-Card (wurde durch TASK-01 ersetzt)
- Im Expander: korrekte Progress Bar „Ausgezahlt: 14,74 € von 15,00 €" mit Label
- `width: (ausgezahlt / vereinbart * 100)%`

**Option B: Progress Bar komplett entfernen:**
- Nur wenn Option A technisch komplex — Text-Info reicht

Entscheide nach TASK-01-Ergebnis und dokumentiere in `04_ux-refactoring-eltern-ansicht_DECISIONS.md`.

**Verifikation:**
- [x] Task erledigt
- Progress Bar hat klares Label oder ist entfernt
- Browser-Test T-07 bestanden

---

### TASK-10 🟡 Jahresübersicht mobile-optimieren
**Prio:** Medium | **Aufwand:** ~4h | **Audit:** Fix #10

**Beschreibung:**  
5-Spalten-Tabelle auf Mobile (< 600px) ist schlecht lesbar. Lösung: Card-Layout auf Mobile.

**Konkret:**
```css
@media (max-width: 600px) {
  .jahresuebersicht-table { display: none; }
  .jahresuebersicht-cards { display: block; }
}
@media (min-width: 601px) {
  .jahresuebersicht-table { display: table; }
  .jahresuebersicht-cards { display: none; }
}
```

Die Cards rendern die gleichen Daten wie die Tabelle, aber vertikal statt horizontal.

**Verifikation:**
- [x] Task erledigt
- DevTools → Viewport auf 375px setzen → Jahresübersicht aufrufen → gut lesbar
- Browser-Test T-10 bestanden

---

## Optional — Low Priority (nur wenn Zeit bleibt)

Die folgenden Fixes stehen im Audit als #11–#14 und sind **nicht Teil** der Abnahme-Kriterien. Nur angehen wenn alle 10 Haupt-Tasks abgeschlossen sind.

- **TASK-11:** „Gespeichert in Firebase" → „✓ Gespeichert" (~30min)
- **TASK-12:** Monatspfeile mit Tooltips/Labels (~1h)
- **TASK-13:** Stift-Icon mit Tooltip oder Label (~1h)
- **TASK-14:** „Abmelden"-Button weniger prominent (~1h)

---

## Abschluss-Checkliste

- [ ] Alle 10 Haupt-Tasks erledigt und verifiziert (TASK-01 bis TASK-10)
- [ ] `COMMENTS.md` hat [PENDING]-Einträge für alle Änderungs-Sessions
- [ ] `03_ux-refactoring-eltern-ansicht_PROGRESS.md` ist aktuell
- [ ] Kein JS-Fehler in der Browser-Console
- [ ] Maxi hat `./git-sync.sh` ausgeführt (nach jeder Phase empfohlen)
