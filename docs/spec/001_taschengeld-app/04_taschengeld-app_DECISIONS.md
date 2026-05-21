# DECISIONS — 001 Taschengeld App

> Jede Entscheidung ist `D-NNN`. Offene Fragen sind `D-OQ-NNN`. Eine offene Frage
> zur Entscheidung befördern: Präfix ändern und eine „Geklärt"-Zeile hinzufügen.

---

## Geklärt

### D-001 — Backend: Firebase Realtime DB (Test-Modus)
- **Kontext:** Ein kostenfreies, einrichtungsfreies Backend benötigt, das aus einer statischen
  HTML-Datei ohne API-Schlüssel erreichbar ist.
- **Entscheidung:** Firebase Realtime Database, Test-Modus-Regeln, ein einziges
  Dokument unter `/taschengeld.json`. Keine Authentifizierung.
- **Warum:** Einfachste REST-API. Kein Build-Schritt. Keine Schlüssel im Quellcode.
- **Kompromiss:** Datenbank ist öffentlich lesbar/schreibbar für jeden, der die
  URL kennt. Für eine private Familien-App akzeptabel.
- **Geklärt:** Vor dieser Spec (übernommen aus `../taschengeld-kontext.md`).

### D-002 — Frontend: Vanilla HTML/CSS/JS, eine Datei
- **Kontext:** Etwas benötigt, das Maxi ohne Toolchain bearbeiten kann.
- **Entscheidung:** Eine `index.html`. Imperatives DOM-Building über einen kleinen
  `h(tag, attrs, children)` Helper. Kein Framework, kein Bundler.
- **Warum:** Kein Build-Schritt → GitHub Pages serviert die Datei unverändert.
- **Kompromiss:** Kein JSX, keine Komponenten-Wiederverwendung außer Funktionen.
  Bei ~500 Zeilen Dateigröße akzeptabel.

### D-003 — Hosting: GitHub Pages auf `main`
- **Kontext:** Kostenloses Hosting benötigt.
- **Entscheidung:** GitHub Pages, öffentliches Repo `maxiholland/taschengeld`,
  serviert vom `main`-Branch-Root.
- **Kompromiss:** Keine Staging-Umgebung. Jeder Push auf `main` deployt.

### D-004 — Auth: PIN in localStorage
- **Kontext:** Eine reibungsarme Möglichkeit benötigt, um die Elternansicht zu sperren.
- **Entscheidung:** 4+ stellige PIN gespeichert in `localStorage` unter `tg-pin`.
  Keine Backend-Beteiligung.
- **Warum:** Ausreichend für ein Familiengerät. Kein Kontosystem benötigt.
- **Kompromiss:** Cache-Leerung löscht die PIN. Wiederherstellungsflow: „PIN vergessen"
  setzt sie zurück. PIN schützt nicht die DB selbst, nur die UI.

### D-005 — Git/Commit-Workflow: `git-sync.sh` + COMMENTS.md
- **Kontext:** Maxi möchte, dass Agenten Code bearbeiten, aber keine Git-Befehle ausführen.
- **Entscheidung:** Agenten bearbeiten nur Dateien (inkl. `COMMENTS.md`).
  Maxi führt `./git-sync.sh` aus, um ausstehende Einträge in einen Commit
  von `Maxi Holland <maxiholland@gmail.com>` zu bündeln.
- **Konfliktstrategie:** Lokal gewinnt (`merge -X ours`, Fallback `checkout --ours`).
- **Meta-Dateien ausgeschlossen:** `git-sync.sh`, `COMMENTS.md`, `CLAUDE.md`
  sind in `.gitignore`. Sie leben nur lokal.

### D-006 — Spec liegt in `code/docs/spec/`
- **Kontext:** Maxi möchte die Spec nah am Code, nicht im übergeordneten
  Ordner mit `taschengeld-kontext.md`.
- **Entscheidung:** Spec liegt unter `code/docs/spec/001_taschengeld-app/` und
  wird ins Repo committet.
- **Warum:** Reist mit dem Quellcode mit. Öffentlich ist in Ordnung — Spec enthält keine
  Geheimnisse außer der bereits öffentlichen Firebase-URL.

### D-007 — Übertrag zählt inaktive Monate als Guthaben
- **Kontext:** Das aktuelle `getCarryOver` des Codes iteriert nur Monate, die
  mindestens eine Transaktion haben. Monate ohne Aktivität tragen null zum
  laufenden Saldo bei.
- **Entscheidung:** Inaktive Monate **zählen**. Wenn ein Kind 20 €/Monat vereinbart hat
  und keine Transaktionen für diesen Monat vorliegen, akkumulieren 20 € als
  „noch offen" / Guthaben für den nächsten aktiven Monat.
- **Konsequenz:** SPEC.md §6.3 beschreibt das *Ziel*verhalten. Der aktuelle Code
  entspricht dem nicht; Task B2 in `TASKS.md` existiert, um das zu beheben.
- **Geklärt:** 2026-05-17 von Maxi.

### D-008 — Sparziele mit `ziel <= 0` sind absichtlich erlaubt
- **Kontext:** Aktueller Code akzeptiert `0` (oder ungültige Eingabe) als `ziel`. UI
  zeigt sofort „🎉 Ziel erreicht!".
- **Entscheidung:** Wird nicht behoben. Als Feature/No-Op behandelt, nicht als Bug.
- **Geklärt:** 2026-05-17 von Maxi.

### D-009 — Spec wird ins öffentliche Repo committet
- **Kontext:** SPEC.md ist für jeden sichtbar, der das Repo klont.
- **Entscheidung:** Committen. Keine Geheimnisse außer der bereits öffentlichen
  Firebase-URL. Nützliche Dokumentation für jeden Leser.
- **Geklärt:** 2026-05-17 von Maxi.

### D-010 — Wöchentlicher Zahlungsintervall: Faktor 4 pro Monat
- **Kontext:** Beim Anlegen eines Kindes soll wahlweise ein Wochenbetrag statt eines Monatsbetrags vereinbart werden können. Die gesamte bestehende Buchungslogik denkt in Monatsschlüsseln.
- **Entscheidung:** `vereinbartProMonat = wochenbetrag * 4` (feste Vereinfachung). Monate mit 4,3 Wochen werden auf 4 gerundet. Abweichung: ~1,30 € bei 3 €/Woche — für eine Familien-App akzeptabel.
- **Warum:** Verhindert, dass `getCarryOver` und `calcStats` monatsabhängig unterschiedliche Werte liefern, was Erklärungsbedarf erzeugt. Einheitlicher Faktor ist vorhersehbar.
- **Kompromiss:** Benachteiligt Kinder leicht in Monaten mit 5 Wochen. Offene Frage D-OQ-5 für präzise Variante.
- **Geklärt:** 2026-05-21 (UX-Review).

### D-011 — Undo-Toast statt Bestätigungsdialog beim Löschen
- **Kontext:** §A8 hatte „kein Bestätigungsdialog". Auf Mobile mit kleinen Fingern ist versehentliches Löschen wahrscheinlich.
- **Entscheidung:** Kein modaler Dialog, stattdessen: sofortige Löschung + nicht-blockierender Undo-Toast für 5 Sekunden. Fühlt sich schnell an, bietet aber Sicherheitsnetz.
- **Warum:** Modale Dialogs unterbrechen den Flow stärker als nötig. Undo ist das gängige Mobile-Muster (vgl. Gmail, iOS Mail).
- **Kompromiss:** Nutzer, die auf einem anderen Gerät gleichzeitig schauen, sehen die Löschung sofort (kein Warten auf Undo-Ablauf). Akzeptabel da kein Multi-Device-Sync ohnehin.
- **Geklärt:** 2026-05-21 (UX-Review).

### D-012 — Kinder können eigene Ausgaben löschen
- **Kontext:** Bisher konnten weder Kind noch Eltern `ausgabe`-Transaktionen löschen. Tippfehler waren nicht korrigierbar.
- **Entscheidung:** Kinder sehen `[×]` bei ihren eigenen `ausgabe`-Zeilen in der Kindansicht. Inklusive Undo-Toast (D-011). Eltern behalten kein Löschrecht für ausgaben.
- **Warum:** Tippfehler passieren. Das Kind ist der Autor der Ausgabe und soll sie auch korrigieren können.
- **Offene Frage:** Wie identifiziert die Kindansicht „das eigene Kind" wenn mehrere Kinder sichtbar sind? Aktuell keine Kindauswahl vorhanden → Task T10 hat ⚠️ INPUT BENÖTIGT.
- **Geklärt:** 2026-05-21 (UX-Review).

### D-013 — Freie Betragseingabe bei Sparzielen (zusätzlich zu +1/+2/+5)
- **Kontext:** Schnell-Buttons +1/+2/+5 decken keine krummen Beträge ab (z. B. 3,50 € Geburtstagsgeld).
- **Entscheidung:** Zusätzliches Freitextfeld neben den Schnell-Buttons. Beide Wege bleiben erhalten. Freifeld wird nach Bestätigung geleert.
- **Warum:** Additive Erweiterung, kein Entfernen bestehender Interaktionsmuster.
- **Kompromiss:** Minimaler zusätzlicher UI-Aufwand; Layout wird etwas voller.
- **Geklärt:** 2026-05-21 (UX-Review).

### D-014 — Geschwister-Sichtbarkeit: jedes Kind sieht nur sich selbst *(revidiert 2026-05-21)*
- **Kontext:** Ursprünglich (D-014 v1) war geplant, alle Kinder sichtbar zu lassen. Nach weiterem UX-Review und Einführung URL-basierter Kindidentifikation (D-017) wurde dies revidiert.
- **Entscheidung:** Jedes Kind sieht via `/?kind={id}` nur seine eigene Karte. Die Root-URL `/` zeigt nur Kindnamen (keine Beträge) als Einstieg.
- **Warum:** Mit URL-basierter Identifikation ist echte Trennung ohne Login möglich. Ältere Kinder schätzen Privatsphäre gegenüber Geschwistern. Die Einstiegshürde ist gering (einmaliges QR-Scannen).
- **Kompromiss:** Eltern müssen jedem Kind einmalig den QR-Code zeigen. Kinder, die ihre URL verloren haben, können über `/` ihren Namen antippen und neu einrichten.
- **Revidiert:** 2026-05-21.

### D-015 — Empty-State-Onboarding für leere Datenbank
- **Kontext:** Kein definierter Zustand für den Ersteinstieg (leere Firebase). Nutzer sehen eine leere Liste ohne Handlungsaufforderung.
- **Entscheidung:** Beide Ansichten bekommen einen dedizierten Leerzustand. Eltern: CTA „+ Erstes Kind hinzufügen". Kinder: erklärender Hinweistext ohne 🔒-Button (verhindert Verwirrung).
- **Warum:** Erstnutzung ohne Onboarding ist ein häufiger Dropoff-Punkt.
- **Geklärt:** 2026-05-21 (UX-Review).

### D-016 — Kind umbenennen via Tipp auf Name in Eltern-Detailansicht
- **Kontext:** Kein UI vorhanden, um den Namen eines Kindes nach dem Anlegen zu ändern.
- **Entscheidung:** Inline-Edit in der Eltern-Detailansicht (Tipp auf Name / ✎-Icon → Textfeld). Kein separates Modal.
- **Warum:** Inline-Edit ist platzsparender und kontextuell naheliegend; der Name ist ohnehin prominent in der Detailansicht.
- **Kompromiss:** Leeres Feld → keine Aktion (kein Löschen des Namens möglich). Escape bricht ab.
- **Geklärt:** 2026-05-21 (UX-Review).

---

## Offene Fragen

### D-OQ-3 — Timing & Deduplizierung bei Auto-Gutschrift
- **Kontext:** Roadmap-Punkt zur automatischen monatlichen Taschengeld-Buchung.
- **Frage:** Clientseitiger Trigger beim ersten Seitenaufruf eines neuen Monats?
  Wie Duplikate verhindern wenn mehrere Geräte am 1. die App öffnen?
- **Status:** offen. Braucht wahrscheinlich einen Idempotenz-Schlüssel pro
  `${childId}-${monatKey}-auto`.

### D-017 — URL-basierte Kindidentifikation: `/?kind={id}` als Identitätsbeweis
- **Kontext:** Kinder brauchen eine eigene, geschützte Ansicht ohne vollwertiges Login-System.
- **Entscheidung:** Jedes Kind bekommt eine persönliche URL `/?kind={id}` (ID = bestehender Timestamp). Diese URL wird als QR-Code in der Eltern-Detailansicht angezeigt. Wer die URL hat, ist das Kind.
- **Warum:** Passt zur bestehenden `?eltern`-Architektur. Keine neuen Auth-Konzepte. Die ID ist kryptografisch nicht stark, aber für Geschwister nicht erratbar.
- **Sicherheitsmodell:** URL = erster Faktor, Kind-PIN = zweiter Faktor (D-018). Vergleichbar mit dem Eltern-PIN-Modell.
- **Kompromiss:** Wer das entsperrte Kinderhandy findet, sieht die gespeicherte URL. Das Geräte-PIN (Face ID, Android-Sperre) ist der stärkere Schutz davor.
- **Geklärt:** 2026-05-21.

### D-018 — Kind-PIN: localStorage, selbst gesetzt, URL als Reset-Faktor
- **Kontext:** Kinder sollen ihre Ansicht vor unerwünschten Zugriffen (z. B. Geschwister mit dem Handy) schützen können, ohne dass Eltern den PIN kennen oder verwalten müssen.
- **Entscheidung:** Kind setzt PIN selbst beim ersten Öffnen von `/?kind={id}`. PIN in `localStorage["tg-kid-pin-{id}"]` — nicht in Firebase. „PIN vergessen" löscht den localStorage-Eintrag und lässt sofort einen neuen setzen. Die URL ist der Identitätsbeweis, kein weiterer Faktor nötig.
- **Warum:** Eltern sehen den PIN nicht → Autonomie für ältere Kinder. Kein Server-Roundtrip. Konsistent mit dem Eltern-PIN-Prinzip.
- **Kompromiss:** Cache-Leerung löscht den PIN — Kind muss neu einrichten. Da die URL gespeichert bleibt (Lesezeichen), ist das ein kurzer Flow. Bei neuem Gerät: QR-Code erneut scannen, PIN neu setzen.
- **Geklärt:** 2026-05-21.

### D-019 — Root-URL `/` zeigt Kindauswahl (nur Namen)
- **Kontext:** Mit URL-basierter Trennung braucht die Root-URL eine neue Rolle.
- **Entscheidung:** `/` zeigt eine neutrale Einstiegsseite mit den Kindnamen als Tipp-Buttons. Keine Beträge, keine Transaktionen. Tipp → navigiert zu `/?kind={id}`. Kein 🔒-Button (Eltern nutzen `/?eltern`).
- **Warum:** Fallback für Kinder, die ihre direkte URL verloren haben. Vermeidet leere oder verwirrende Seite.
- **Kompromiss:** Namen sind ohne PIN sichtbar — bewusst, da Namen keine sensitiven Daten sind.
- **Geklärt:** 2026-05-21.

### D-020 — QR-Code via qrcode.js (CDN), kein Build-Schritt
- **Kontext:** QR-Code-Generierung in einer reinen HTML-Datei ohne npm.
- **Entscheidung:** `qrcode.js` via CDN (`cdnjs.cloudflare.com`). Client-seitige Generierung, kein Server.
- **Warum:** Einzige externe Abhängigkeit, die bisher nötig wird. CDN-Einbindung ist konsistent mit der Vanilla-Philosophie des Projekts.
- **Kompromiss:** Abhängigkeit von CDN-Verfügbarkeit. Bei CDN-Ausfall kein QR-Code — der Kopier-Button als Fallback bleibt funktionsfähig.
- **Geklärt:** 2026-05-21.

### D-OQ-5 — Wöchentlicher Intervall: tatsächliche Wochenzahl statt Faktor 4
- **Kontext:** D-010 hat Faktor 4 als Vereinfachung festgelegt.
- **Frage:** Soll `wochenImMonat(mk)` die tatsächliche Anzahl ISO-Wochen zählen, die im Monat beginnen (gibt 4 oder 5)? Das würde die Berechnung für 5-Wochen-Monate korrigieren, aber `getCarryOver` macht die Berechnung monat-abhängig.
- **Status:** offen. Erst relevant wenn wöchentlicher Intervall in Betrieb ist und Eltern Präzision einfordern.

### D-OQ-4 — Benachrichtigungs-UX
- **Kontext:** Roadmap-Punkt.
- **Frage:** Browser Notification API (erfordert Berechtigung) oder ein
  passiver In-App-Indikator? Polling-Intervall?
- **Status:** offen.

---

## Rücknahmen / Zurückgezogene Entscheidungen

(noch keine)
