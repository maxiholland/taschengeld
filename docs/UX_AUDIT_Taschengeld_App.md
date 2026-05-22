# UX-Audit: Taschengeld-App (Eltern-Ansicht)

**Audit-Datum:** 2026-05-22  
**Audit-Basis:** Screenshots der Eltern-Ansicht (PIN-Login, Monatsübersicht, Kind-Detailansicht, Jahresübersicht)  
**Zielgruppe:** Eltern, die Taschengeld für ihre Kinder verwalten  

---

## Executive Summary

Die Taschengeld-App ist technisch funktionsfähig, hat aber **signifikante UX-Probleme**, die die Benutzbarkeit stark beeinträchtigen. Die größten Schmerzpunkte:

1. **Kognitiver Overload** — Zu viele Zahlen auf einen Blick, unklar was wichtig ist
2. **Mehrdeutige Negativ-Saldi** — Eltern verstehen nicht, wer wem was schuldet
3. **Fehlende visuelle Hierarchie** — Alle Aktionen sehen gleich wichtig aus
4. **Progress Bar ohne Bedeutung** — Vermittelt keine nützliche Information

**Geschätzter Gesamt-Aufwand für Top-10-Fixes:** ~23 Stunden  
**Quick Wins (≤2h):** 5 Fixes, ~8 Stunden  
**High-Impact-Fixes (Priorität 🔴):** 4 Fixes, ~9 Stunden  

---

## Empfohlene Umsetzungs-Strategie

### Phase 1: Quick Wins (1-2 Tage)
- Fix #3: "Vereinbart" mit Zeiteinheit (1h)
- Fix #4: Rote 0,00 € entfernen (1h)
- Fix #7: Übertrag-Kontext hinzufügen (2h)
- Fix #8: Löschen mit Confirmation (2h)
- Fix #9: QR-Code erklären (1h)

**Ergebnis:** Die schlimmsten Verwirrungen sind weg, App fühlt sich professioneller an.

### Phase 2: High-Impact-Fixes (3-4 Tage)
- Fix #2: Negativ-Saldi klarstellen (3h)
- Fix #1: Info-Overload reduzieren (4h)
- Fix #5: Button-Hierarchie einführen (2h)

**Ergebnis:** Eltern verstehen sofort, was sie tun müssen.

### Phase 3: Optimierungen (2-3 Tage)
- Fix #6: Progress Bar ersetzen (3h)
- Fix #10: Tabelle mobile-optimieren (4h)
- Fix #11-14: Kleinere Verbesserungen (je 1h)

---

## 🔴 KRITISCHE UX-PROBLEME (High Priority)

---

### Problem #1: Kognitiver Overload in der Kind-Card

**Priorität:** 🔴 High  
**Geschätzter Aufwand:** 4 Stunden  
**Betroffener Screen:** Monatsübersicht (Screen 2)  

#### Problem-Beschreibung

Die Kind-Card zeigt gleichzeitig **7 verschiedene Zahlen**:
- Vereinbart: 15,00 €/Monat
- Ausgegeben: 0,00 €
- Noch offen: 0,26 €
- Bargeld: 1,00 €
- Auslagen: 4,99 €
- Übertrag: -8,75 €
- Progress Bar (visuell)

Eltern brauchen 10+ Sekunden, um zu verstehen: **"Muss ich meinem Kind jetzt Geld geben oder nicht?"**

#### User Impact

- **Frustration:** Jeder Blick auf die Übersicht ist kognitiv anstrengend
- **Fehleranfälligkeit:** Eltern übersehen wichtige Informationen
- **Zeitverschwendung:** 10 Sekunden pro Kind → bei 3 Kindern 30 Sekunden nur zum Verstehen

#### Before (aktuell)

```
+----------------------------------------------------------+
| [E]  Ellen                                  noch offen   |
|      15,00 €/Monat · ausgegeben 0,00 €        0,26 €    |
|                                                           |
| ██████████████████████████████████████████░░░ (Progress) |
|                                                           |
| Bargeld 1,00 €    Auslagen 4,99 €    Übertrag -8,75 €   |
+----------------------------------------------------------+
```

**Problem:** Zu viele Datenfelder, keine visuelle Priorisierung, unklar was die wichtigste Info ist.

#### After (gewünscht)

```
+----------------------------------------------------------+
| [E]  Ellen                                               |
|      15,00 € / Monat                                     |
|                                                           |
|      Noch auszuzahlen: 0,26 €              ✓ Fast fertig|
|                                                           |
|      > Details anzeigen                                  |
+----------------------------------------------------------+
```

**Beim Klick auf "Details anzeigen":**

```
+----------------------------------------------------------+
| Bereits ausgezahlt:                                      |
| • Bargeld: 1,00 € (16.05.)                              |
| • Auslagen: 4,99 € (13.05.)                             |
|                                                           |
| Übertrag aus April: -8,75 € (zu viel bekommen)          |
| ────────────────────────────────────────────             |
| Noch auszuzahlen: 0,26 €                                |
+----------------------------------------------------------+
```

#### Konkrete Fixes

1. **Primary Info:** "Noch auszuzahlen: X,XX €" prominent + Farb-Coding
   - Grün wenn >0: "Noch auszuzahlen: 8,00 €" → Kind bekommt Geld
   - Neutral/Grau wenn =0: "✓ Alles ausgezahlt"
   - Rot wenn <0: "Überzahlt: 8,75 €" → Kind schuldet Eltern

2. **Sekundär-Info wegklappen:** Bargeld, Auslagen, Übertrag in "Details anzeigen"-Expander

3. **Progress Bar entfernen** (oder vereinfachen zu Icon)

4. **Vereinbart nur als Kontext:** Kleiner, grauer Text: "15,00 € / Monat"

#### Verifikation

- [ ] User kann in ≤3 Sekunden sagen: "Ich muss noch X Euro auszahlen"
- [ ] Card zeigt maximal 3 Datenfelder gleichzeitig
- [ ] "Details anzeigen" funktioniert und ist intuitiv

---

### Problem #2: Verwirrende Negativ-Saldi

**Priorität:** 🔴 High  
**Geschätzter Aufwand:** 3 Stunden  
**Betroffene Screens:** Monatsübersicht, Kind-Detailansicht, Jahresübersicht  

#### Problem-Beschreibung

Negativ-Beträge wie **"-8,75 €"** oder **"-9,01 €"** sind mehrdeutig:
- Schuldet das Kind den Eltern Geld?
- Schulden die Eltern dem Kind Geld?
- Ist das eine Überzahlung?
- Ist das ein Guthaben oder ein Minus?

**Aktuell gibt es KEINE Erklärung**, was das Vorzeichen bedeutet.

#### User Impact

- **Fundamentales Verständnisproblem:** Eltern wissen nicht, ob sie Geld geben oder zurückhalten sollen
- **Vertrauensverlust:** "Die App ist kaputt" oder "Die Zahlen stimmen nicht"
- **Fehlentscheidungen:** Eltern zahlen zu viel oder zu wenig aus

#### Before (aktuell)

**Jahresübersicht:**
```
Mai     15,00 €    1,00 €    4,99 €    -9,01 €
Gesamt  30,00 €    1,00 €    28,74 €   -0,26 €
```

**Frage:** Was bedeutet "-9,01 €"? Was bedeutet "-0,26 €"?

**Kind-Detailansicht:**
```
Übertrag aus Vormonat: -8,75 €
```

**Frage:** Ist das gut oder schlecht? Soll ich das ausgleichen?

#### After (gewünscht)

**Variante A: Explizite Labels**

```
Mai     15,00 €    1,00 €    4,99 €    Im Minus: 9,01 €
Gesamt  30,00 €    1,00 €    28,74 €   Kind schuldet: 0,26 €
```

**Variante B: Kontext im Tooltip/Subtext**

```
Mai     15,00 €    1,00 €    4,99 €    -9,01 € ⓘ
                                        Kind hat 9,01 € zu viel ausgegeben
```

**Kind-Detailansicht:**

```
Übertrag aus Vormonat: -8,75 €
⚠️ Kind hat im April 8,75 € zu viel bekommen.
   Wird vom Mai-Taschengeld abgezogen.
```

#### Konkrete Fixes

1. **Spalten-Header umbenennen:**
   - Statt "SALDO" → "SALDO (+ Kind bekommt / - Kind schuldet)"
   - Oder zwei Spalten: "KIND BEKOMMT" und "KIND SCHULDET"

2. **Negativ-Beträge explizit beschriften:**
   - Statt "-9,01 €" → "Kind schuldet: 9,01 €" oder "Überzahlt: 9,01 €"

3. **Farb-Coding konsistent:**
   - Grün: Kind hat Guthaben (bekommt Geld)
   - Rot: Kind hat Schulden (schuldet Geld)
   - Neutral/Grau: Ausgeglichen

4. **Übertrag mit Erklärung:**
   - Info-Icon mit Tooltip
   - Oder ausklappbarer Text

#### Verifikation

- [ ] User kann ohne Nachdenken sagen: "Mein Kind bekommt noch X Euro" oder "Mein Kind schuldet mir X Euro"
- [ ] Kein negativer Betrag ohne explizite Beschriftung
- [ ] Tooltip/Erklärung für "Übertrag" vorhanden

---

### Problem #3: "Vereinbart" ohne Zeiteinheit

**Priorität:** 🔴 High  
**Geschätzter Aufwand:** 1 Stunde  
**Betroffener Screen:** Kind-Detailansicht (Screen 3)  

#### Problem-Beschreibung

In der **Kind-Detailansicht** steht:
```
Vereinbart: 15,00 €
```

**Aber:** Ist das pro Monat? Pro Woche? Pro Jahr?

In der **Monatsübersicht** steht zwar "15,00 €/Monat", aber in der Detailansicht fehlt die Zeiteinheit.

#### User Impact

- **Mehrdeutigkeit:** Eltern müssen raten oder zurücknavigieren
- **Inkonsistenz:** Unterschiedliche Darstellung auf verschiedenen Screens

#### Before (aktuell)

```
Vereinbart: 15,00 €
Bargeld: 1,00 €
```

#### After (gewünscht)

```
Vereinbart: 15,00 € / Monat
Bargeld: 1,00 €
```

Oder noch besser:

```
Monatliches Taschengeld: 15,00 €
Bereits ausgezahlt (Bargeld): 1,00 €
```

#### Konkrete Fixes

1. Immer Zeiteinheit anzeigen: `15,00 € / Monat` oder `2,00 € / Woche`
2. Oder Label umbenennen: "Monatliches Taschengeld" / "Wöchentliches Taschengeld"

#### Verifikation

- [ ] Zeiteinheit ist immer sichtbar
- [ ] Konsistent über alle Screens

---

### Problem #4: "Kind ausgegeben: 0,00 €" in ROT ist irreführend

**Priorität:** 🔴 High  
**Geschätzter Aufwand:** 1 Stunde  
**Betroffener Screen:** Kind-Detailansicht (Screen 3)  

#### Problem-Beschreibung

```
Kind ausgegeben: 0,00 €   <-- in ROT
```

**Rot signalisiert:** Problem, Fehler, Warnung  
**Aber:** 0,00 € ausgeben ist eigentlich **gut** (Kind spart)  

#### User Impact

- **False Alarm:** Eltern denken, etwas ist falsch
- **Verwirrung:** Warum ist "nichts ausgeben" ein Problem?

#### Before (aktuell)

```
Kind ausgegeben: 0,00 €   [ROT]
```

#### After (gewünscht)

```
Kind ausgegeben: 0,00 €   [GRAU/NEUTRAL]
```

Oder bei negativem Saldo:

```
Kind ausgegeben: 15,00 €   [ROT, wenn > Vereinbart]
Kind ausgegeben: 10,00 €   [GRÜN, wenn < Vereinbart]
```

#### Konkrete Fixes

1. **Farb-Logik ändern:**
   - Rot nur wenn: `ausgegeben > vereinbart` (Überzug)
   - Grün wenn: `ausgegeben < vereinbart` (Sparen)
   - Grau/Neutral wenn: `ausgegeben = 0` oder `ausgegeben = vereinbart`

2. **Alternativ:** Diese Zeile ganz weglassen, stattdessen nur "Noch offen" zeigen

#### Verifikation

- [ ] Rot nur bei tatsächlichen Problemen (Überzug)
- [ ] 0,00 € = neutrales Grau

---

## 🟡 WICHTIGE UX-PROBLEME (Medium Priority)

---

### Problem #5: Buttons ohne klare Hierarchie

**Priorität:** 🟡 Medium  
**Geschätzter Aufwand:** 2 Stunden  
**Betroffener Screen:** Kind-Detailansicht (Screen 3)  

#### Problem-Beschreibung

```
[ Bargeld ausgezahlt ]   [ Auslage verbuchen ]
```

Beide Buttons sehen **identisch** aus, aber "Bargeld ausgezahlt" ist die **primäre Aktion** (häufigste User-Intention), "Auslage verbuchen" ist sekundär.

#### User Impact

- **Keine Orientierung:** User muss lesen, um zu entscheiden
- **Langsamere Interaktion:** Keine visuellen Shortcuts

#### Before (aktuell)

```
┌──────────────────────┐  ┌──────────────────────┐
│ Bargeld ausgezahlt   │  │ Auslage verbuchen    │
└──────────────────────┘  └──────────────────────┘
```

#### After (gewünscht)

```
┌──────────────────────┐  ┌──────────────────────┐
│ Bargeld ausgezahlt   │  │ Auslage verbuchen    │  <-- Outline
└──────────────────────┘  └──────────────────────┘
   ^ Filled/Primary          ^ Secondary
```

#### Konkrete Fixes

1. **Primary Button** (gefüllt, farbig): "Bargeld ausgezahlt"
2. **Secondary Button** (outline, grau): "Auslage verbuchen"

#### Verifikation

- [ ] Primary Action ist visuell prominent
- [ ] User kann Haupt-Aktion ohne Lesen erkennen

---

### Problem #6: Progress Bar ist nutzlos/verwirrend

**Priorität:** 🟡 Medium  
**Geschätzter Aufwand:** 3 Stunden  
**Betroffener Screen:** Monatsübersicht, Kind-Detailansicht  

#### Problem-Beschreibung

**Screen 2 (Ellen):** Progress Bar ist **fast voll** (blau), aber "noch offen 0,26 €"  
**Screen 2 (Adrian):** Progress Bar ist **komplett leer** (grau), aber "noch offen 8,00 €"  

**Frage:** Was bedeutet der Progress Bar?
- Wie viel wurde ausgezahlt?
- Wie viel wurde ausgegeben?
- Wie nah bin ich am Ziel?

**Problem:** Inkonsistent und vermittelt keine nützliche Information.

#### User Impact

- **Verwirrung:** User versteht nicht, was der Bar bedeutet
- **Visuelle Ablenkung:** Lenkt von wichtiger Info ab

#### Before (aktuell)

```
Ellen:  ████████████████████████████░░  <-- fast voll, aber nur 0,26 € offen?
Adrian: ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  <-- leer, aber 8,00 € offen?
```

#### After (gewünscht)

**Option A: Progress Bar korrekt interpretieren**

```
Ellen:  Ausgezahlt: 14,74 € von 15,00 €
        ████████████████████████████░░  (98%)

Adrian: Ausgezahlt: 0,00 € von 8,00 €
        ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  (0%)
```

**Option B: Progress Bar entfernen, Text-Info stattdessen**

```
Ellen:  Bereits ausgezahlt: 14,74 € von 15,00 €

Adrian: Noch auszuzahlen: 8,00 € (nichts ausgezahlt)
```

#### Konkrete Fixes

1. **Entweder:** Progress Bar zeigt korrekt "Ausgezahlt / Vereinbart"
   - 0% = nichts ausgezahlt
   - 100% = alles ausgezahlt
   - Mit Label: "Ausgezahlt: X € von Y €"

2. **Oder:** Progress Bar komplett entfernen, klare Text-Info stattdessen

#### Verifikation

- [ ] Progress Bar hat klare Bedeutung (oder ist weg)
- [ ] User versteht ohne Raten, was der Bar bedeutet

---

### Problem #7: "Übertrag aus Vormonat" fehlt Kontext

**Priorität:** 🟡 Medium  
**Geschätzter Aufwand:** 2 Stunden  
**Betroffener Screen:** Kind-Detailansicht (Screen 3)  

#### Problem-Beschreibung

```
Übertrag aus Vormonat: -8,75 €   [gelber Hintergrund]
```

**Fragen:**
- Warum ist das gelb? (Warnung? Info? Highlight?)
- Was bedeutet der Negativ-Wert? (siehe Problem #2)
- Kann man das auflösen/bearbeiten/korrigieren?

#### User Impact

- **Unklarheit:** User versteht nicht, was das bedeutet
- **Keine Handlungsoption:** Wenn das ein Problem ist, wie löst man es?

#### Before (aktuell)

```
Übertrag aus Vormonat: -8,75 €
```

#### After (gewünscht)

**Option A: Mit Tooltip**

```
Übertrag aus Vormonat: -8,75 € ⓘ
    ↓ (beim Hover)
┌─────────────────────────────────────┐
│ Kind hat im April 8,75 € zu viel    │
│ bekommen. Wird vom Mai-Taschengeld  │
│ abgezogen.                           │
│                                      │
│ > April-Details anzeigen            │
└─────────────────────────────────────┘
```

**Option B: Mit ausklappbarem Text**

```
⚠️ Übertrag aus April: -8,75 €
   Kind hat im April 8,75 € zu viel bekommen.
   Wird vom Mai-Taschengeld abgezogen.
   > Vormonats-Details anzeigen
```

#### Konkrete Fixes

1. Info-Icon mit Tooltip: Erklärt, was der Übertrag bedeutet
2. Klickbar → zeigt Vormonats-Details
3. Gelber Hintergrund: Nur wenn es eine Warnung ist (negativ), sonst neutral

#### Verifikation

- [ ] Tooltip oder Erklärung vorhanden
- [ ] Link zu Vormonats-Details funktioniert

---

### Problem #8: Transaktionen: X-Button ohne Confirmation

**Priorität:** 🟡 Medium  
**Geschätzter Aufwand:** 2 Stunden  
**Betroffener Screen:** Kind-Detailansicht (Screen 3)  

#### Problem-Beschreibung

Jede Transaktion hat ein **X zum Löschen**, aber **kein "Bist du sicher?"-Dialog**.

```
Taschengeld Mai    1,00 €   [X]   <-- ein Klick = weg
Einmalhandschuhe   4,99 €   [X]
```

#### User Impact

- **Versehentliches Löschen:** Ein falscher Klick = Datenverlust
- **Keine Undo-Möglichkeit:** Aktion ist sofort final
- **Falsche Abrechnungen:** Wenn Transaktion versehentlich gelöscht wird

#### Before (aktuell)

```
Taschengeld Mai    1,00 €   [X]
    ↓ (ein Klick)
[Transaktion gelöscht]  <-- sofort, ohne Warnung
```

#### After (gewünscht)

**Option A: Confirmation Modal**

```
Taschengeld Mai    1,00 €   [X]
    ↓ (klick)
┌─────────────────────────────────────┐
│ Transaktion wirklich löschen?       │
│                                      │
│ Taschengeld Mai (1,00 €)            │
│                                      │
│ [ Abbrechen ]  [ Löschen ]          │
└─────────────────────────────────────┘
```

**Option B: Undo-Toast**

```
Taschengeld Mai    1,00 €   [X]
    ↓ (klick)
[Transaktion wurde gelöscht]  [Rückgängig]  <-- 5 Sekunden Zeit
```

#### Konkrete Fixes

1. **Option A:** Modal-Dialog: "Transaktion 'X' wirklich löschen?"
2. **Option B:** Undo-Toast nach Löschen (5 Sekunden)
3. **Alternativ:** Trash-Icon statt X, signalisiert "vorsichtig"

#### Verifikation

- [ ] Löschen erfordert Bestätigung
- [ ] Oder: Undo-Funktion vorhanden

---

### Problem #9: "Kind-Link teilen" ohne Erklärung

**Priorität:** 🟡 Medium  
**Geschätzter Aufwand:** 1 Stunde  
**Betroffener Screen:** Kind-Detailansicht (Screen 3)  

#### Problem-Beschreibung

```
Kind-Link teilen
[QR-Code]
```

**Fragen:**
- Was passiert, wenn ich den QR-Code teile?
- Kann das Kind dann Transaktionen hinzufügen?
- Geld abheben?
- Die App bearbeiten?

**Aktuell gibt es KEINE Erklärung.**

#### User Impact

- **Unsicherheit:** Eltern wissen nicht, was sie freigeben
- **Sicherheitsbedenken:** "Kann mein Kind dann alles ändern?"

#### Before (aktuell)

```
Kind-Link teilen
████████████████
████        ████
████████████████
```

#### After (gewünscht)

```
Kind-App-Zugang teilen

Mit diesem QR-Code kann [Kind-Name] ihre
Ausgaben selbst eintragen und ihr Guthaben
sehen — aber nicht bearbeiten oder löschen.

████████████████
████        ████
████████████████

> Was kann mein Kind damit machen?
```

#### Konkrete Fixes

1. **Expliziter Text:**
   - "QR-Code scannen lassen, damit [Kind-Name] ihre Ausgaben selbst eintragen kann"
   - "Kind-App-Zugang" statt "Kind-Link"

2. **Info-Dialog:** "Was kann mein Kind mit diesem Link machen?"
   - Anzeigen: ✓ Guthaben sehen, ✓ Ausgaben eintragen
   - NICHT: ✗ Transaktionen löschen, ✗ Taschengeld ändern

#### Verifikation

- [ ] Erklärung vorhanden: Was kann das Kind mit dem Link?
- [ ] Link/Button beschreibend benannt ("Kind-App-Zugang" statt nur "Link")

---

### Problem #10: Jahresübersicht: Tabelle nicht mobile-optimiert

**Priorität:** 🟡 Medium  
**Geschätzter Aufwand:** 4 Stunden  
**Betroffener Screen:** Jahresübersicht (Screen 4)  

#### Problem-Beschreibung

Die Tabelle hat **5 Spalten** auf einem Smartphone-Screen:

```
MONAT | VEREINB. | BARGELD | AUSLAGEN | SALDO
```

**Problem:** Text wird zu klein oder bricht um, schwer lesbar auf Mobile.

#### User Impact

- **Schlechte Lesbarkeit:** User muss zoomen oder horizontal scrollen
- **Keine Mobile-First-Optimierung**

#### Before (aktuell)

```
┌────────┬──────┬────────┬──────────┬────────┐
│ MONAT  │ VER. │ BARG.  │ AUSLAG.  │ SALDO  │  <-- 5 Spalten
├────────┼──────┼────────┼──────────┼────────┤
│ Januar │ -    │ 0,00 € │ 0,00 €   │ +0,00 €│
│ ...    │      │        │          │        │
└────────┴──────┴────────┴──────────┴────────┘
```

Auf Mobile: Text wird gequetscht oder scrollt horizontal.

#### After (gewünscht)

**Option A: Cards statt Tabelle (Mobile)**

```
┌─────────────────────────────────────┐
│ Januar 2026                          │
│ Vereinbart: - | Ausgezahlt: 0,00 €  │
│ Saldo: +0,00 €                       │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│ April 2026                           │
│ Vereinbart: 15,00 € | Ausgezahlt: 23,75 €│
│ Saldo: +8,75 €                       │
└─────────────────────────────────────┘
```

**Option B: Horizontales Scrollen mit fixierter "MONAT"-Spalte**

```
┌────────┐┌──────┬────────┬──────────┬────────┐
│ MONAT  ││ VER. │ BARG.  │ AUSLAG.  │ SALDO  │
├────────┤├──────┼────────┼──────────┼────────┤
│ Januar ││ -    │ 0,00 € │ 0,00 €   │ +0,00 €│
│ (fix)  ││ (scrollt horizontal) ────────────>│
```

#### Konkrete Fixes

1. **Responsive Design:** Auf Mobile (<600px) → Cards statt Tabelle
2. Oder: Horizontales Scrollen mit fixierter erster Spalte
3. Oder: Reduzierte Spalten auf Mobile (nur Monat, Saldo)

#### Verifikation

- [ ] Tabelle ist auf Mobile gut lesbar (kein Zoomen nötig)
- [ ] Responsive Breakpoint funktioniert

---

## 🟢 KLEINERE VERBESSERUNGEN (Low Priority)

---

### Problem #11: "Gespeichert in Firebase" ist Developer-Sprache

**Priorität:** 🟢 Low  
**Geschätzter Aufwand:** 30 Minuten  
**Betroffener Screen:** Monatsübersicht (Screen 2)  

#### Problem

```
● Gespeichert in Firebase
```

**Firebase** ist Tech-Jargon. Normale User kennen das nicht.

#### Fix

- "✓ Gespeichert" (einfach)
- Oder: "☁️ In der Cloud gesichert"
- Oder: Komplett entfernen (sollte unsichtbar funktionieren)

---

### Problem #12: Navigation: Pfeile ohne Label

**Priorität:** 🟢 Low  
**Geschätzter Aufwand:** 1 Stunde  
**Betroffener Screen:** Alle Screens mit Monats-Navigation  

#### Problem

```
<   Mai 2026   >
```

Wohin führen die Pfeile? Vorheriger Monat? Nächster Monat?

#### Fix

- Tooltips beim Hover: "< April 2026" und "Juni 2026 >"
- Oder Mini-Labels: "< April" und "Juni >"

---

### Problem #13: Stift-Icon ohne erkennbare Funktion

**Priorität:** 🟢 Low  
**Geschätzter Aufwand:** 1 Stunde  
**Betroffener Screen:** Kind-Detailansicht (Screen 3)  

#### Problem

```
Ellen                           🖊️
```

Was kann ich damit bearbeiten? Alles? Nur den Namen?

#### Fix

- Beim Tippen: Edit-Formular mit klaren Feldern
- Oder: Label "Bearbeiten" statt nur Icon
- Oder: Tooltip: "Kind-Details bearbeiten"

---

### Problem #14: "Abmelden" Button zu prominent

**Priorität:** 🟢 Low  
**Geschätzter Aufwand:** 1 Stunde  
**Betroffener Screen:** Monatsübersicht (Screen 2)  

#### Problem

```
🔒 Abmelden   <-- prominenter Button oben rechts
```

Logout ist keine häufige Aktion, aber bekommt viel Platz.

#### Fix

- Hamburger-Menü oder Profil-Icon → Logout darin verstecken
- Mehr Platz für wichtigere Actions

---

## Priorisierte Fix-Liste (Summary)

| Prio | ID | Problem | Aufwand | Empfohlene Phase |
|------|----|---------|---------|------------------|
| 🔴 | #1 | Info-Overload in Cards | 4h | Phase 2 |
| 🔴 | #2 | Negativ-Saldi unklar | 3h | Phase 2 |
| 🔴 | #3 | "Vereinbart" ohne Zeiteinheit | 1h | Phase 1 |
| 🔴 | #4 | Rote 0,00 € irreführend | 1h | Phase 1 |
| 🟡 | #5 | Button-Hierarchie fehlt | 2h | Phase 2 |
| 🟡 | #6 | Progress Bar nutzlos | 3h | Phase 3 |
| 🟡 | #7 | Übertrag-Kontext fehlt | 2h | Phase 1 |
| 🟡 | #8 | Löschen ohne Confirmation | 2h | Phase 1 |
| 🟡 | #9 | QR-Code ohne Erklärung | 1h | Phase 1 |
| 🟡 | #10 | Tabelle nicht mobile-optimiert | 4h | Phase 3 |
| 🟢 | #11-14 | Kleinere Verbesserungen | 3,5h | Phase 3 |

**Gesamt-Aufwand:** ~26,5 Stunden

---

## Nutzung dieses Dokuments in Cowork

### Als Input für den Spec-Writer-Skill

Du kannst dieses Dokument direkt als Input für den verbesserten Spec-Writer-Skill in Cowork nutzen:

**Prompt für Cowork:**

```
Erstelle eine UX-Refactoring-Spec für die Taschengeld-App basierend auf dem
UX-Audit-Dokument. Die Spec soll alle 10 Probleme als separate Tasks enthalten,
sortiert nach Priorität (🔴 → 🟡 → 🟢). 

Context:
- Bestehende Taschengeld-App (React + Firebase)
- Zielgruppe: Eltern, die Taschengeld für Kinder verwalten
- Tech-Stack: [Details aus deinem Projekt einfügen]

Feature-Name: UX-Refactoring Taschengeld-App (Eltern-Ansicht)

Probleme: [Dieses Dokument als Kontext anhängen oder reinkopieren]
```

Der Skill wird dann automatisch:
1. Eine neue Spec `00X_ux-refactoring-taschengeld/` anlegen
2. SPEC.md mit allen UX-Problemen füllen
3. TASKS.md mit 10 priorisierten Tasks erstellen
4. ASCII-Wireframes und Mermaid-Diagramme integrieren

### Quick-Start-Workflow

1. **Verbesserten Skill in Cowork importieren** (SKILL_IMPROVED.md)
2. **UX-Audit-Dokument öffnen** (diese Datei)
3. **In Cowork:** "Erstelle Spec basierend auf UX-Audit"
4. **Skill arbeitet:** Erstellt vollständige Spec mit Tasks
5. **Übergabe:** Spec an Implementations-Agent (Claude Code, Cursor)

---

## Anhang: ASCII-Wireframes für alle Screens

### Before/After Wireframes werden in der Spec integriert

Alle oben gezeigten Before/After-Wireframes können direkt in SPEC.md §10 übernommen werden.
