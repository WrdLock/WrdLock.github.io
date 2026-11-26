# Tumbler: das tägliche Schloss

Ein tägliches Browser-Wortspiel. Jeder Spieler bekommt am selben Tag dieselben sieben Schlösser. Jedes Schloss hat 3 bis 5 Stifte, das sind Hinweise auf ein verstecktes deutsches Wort. Du schreibst ein echtes Wort, das alle Stifte erfüllt.

## Starten

`index.html` im Browser öffnen. Es gibt nichts zu installieren und keinen Server. Alles steckt in dieser einen Datei, auch das Wörterbuch. Die Schriften kommen von Google Fonts, ohne Internet greift ein Standard-Schriftersatz.

## Spielablauf

- Drei Stufen, jede mit eigenem Tagesschloss und eigener Wertung: **Leicht**, **Mittel**, **Schwer**.
- Sieben Schlösser pro Tag, 30 Sekunden pro Schloss (Leicht: 35).
- Beispiele für Stifte: „Beginnt mit M“ oder „Beginnt mit Fe“, „Genau 6 Buchstaben“, „Endet mit E“ oder „Endet mit ch“, „Enthält ei“, „4. Buchstabe ist n“, „Ohne den Buchstaben d“, „Genau 3 Vokale“, „Genau 5 verschiedene Buchstaben“, „Der Buchstabe e kommt genau 2-mal vor“, „Enthält den Buchstaben k“, „Enthält einen Umlaut“ oder „Enthält einen Doppelbuchstaben“.
- Die Hinweise kommen immer vom versteckten Wort, jedes Schloss ist daher lösbar. Seltene Endbuchstaben wie h werden nicht einzeln abgefragt, dann steht dort stattdessen „Endet mit ch“. Hinweise, die sich widersprechen oder doppeln, werden nicht kombiniert.
- Die Stifte setzen sich live, während du tippst.
- Ein Wort, das es nicht gibt, kann nicht abgegeben werden.

| Stufe | Zeit | Stifte pro Schloss | Wortlänge |
|---|---|---|---|
| Leicht | 35 s | immer 3 | kurze Wörter |
| Mittel | 30 s | 3 bis 4 | kurze bis mittlere Wörter |
| Schwer | 30 s | 3 bis 4 | auch längere Wörter |

Pro Schloss gibt es höchstens einen schweren Hinweis (Buchstabenpaar, Position, Häufigkeit, verschiedene Buchstaben, Endung). Nur die letzten Schlösser der Stufe Schwer dürfen zwei haben. Es werden nur gängige Buchstabenpaare wie er, en, ch, ei, au, st gefragt.

Unten rechts gibt es Schalter für Hell/Dunkel und Ton an/aus. Beide Einstellungen werden gespeichert.

## Punkte

- 40 Punkte pro gesetztem Stift.
- Sind alle Stifte gesetzt, öffnet das Schloss: +60 und 2 Punkte pro Restsekunde.
- Wer genau das Lösungswort trifft: +40 Volltreffer.
- Ein Wort ohne Treffer oder ein Timeout gibt 0 Punkte.

## Tägliche Herausforderung

- Die Aufgaben werden nur aus dem Datum und der Stufe berechnet (UTC), nicht aus Zufall pro Besuch. Gleiches Datum bedeutet gleiche Schlösser für alle.
- Tag Nr. 1 ist der 25.11.2026. Der Reset ist täglich um 00:00 UTC, der Countdown auf dem Startbildschirm zeigt die Restzeit.
- Ein offizieller Versuch pro Tag und Stufe. Ein Neuladen erzeugt keinen zweiten Versuch. Wer mitten in einem Schloss die Seite verlässt, verliert dieses Schloss.
- Das Übungsschloss (zufällig, unbegrenzt) zählt nicht für Statistik und Serie.

## Statistik und Teilen

Alles wird lokal im Browser gespeichert (`localStorage`, Schlüssel `tumbler:v4`): Serie, Bestwert, Schnitt, Anzahl Schlösser, Gesamtpunkte, Logbuch und eine **lokale** Rangliste. Es gibt keine globale Rangliste und keine Spielerdatenbank.

Nach dem Spiel gibt es einen spoilerfreien Text zum Kopieren, zum Beispiel:

```
Tumbler #1 Mittel · 1240
🟩 🟩 🟨 🟩 🟥 🟨 🟩
Stifte 19/25
```

## Wörterbuch

Die Wortprüfung läuft komplett lokal, ohne KI, ohne API-Schlüssel und ohne Netzwerkanfragen.

- Quelle: [spielwoerter](https://github.com/marekventur/spielwoerter) von Marek Ventur, Datei `wordlist_accepted.jsonl`, CC0.
- 239.391 Wortformen in Kleinschreibung (mit ä, ö, ü, ß), eingebettet als Textblock in `index.html` (`<script id="wl" type="text/plain">`).
- Die Prüfung ist ein `Set`-Lookup (Laden etwa 85 ms, 100.000 Prüfungen etwa 14 ms in einem Node-Test).
- Wörter über 9 Buchstaben, die nicht in der Liste stehen, zählen, wenn sie sich aus Listenwörtern zusammensetzen (Teile ab 3 Buchstaben, Fugenlaute s, es, n, en, e, er, ens erlaubt).
- Das versteckte Lösungswort einer Runde gilt immer als gültig.

## Bekannte Grenzen

- „strasse“ wird nicht als „straße“ akzeptiert.
- Die Zerlegung langer Wörter ist eine Heuristik. Eine Zusammensetzung aus echten Wörtern wird akzeptiert, auch wenn sie ungewöhnlich ist.
- Alles ist lokal. Es gibt keinen Server, daher keine echten Vergleiche zwischen Spielern und keinen Schutz davor, den Speicher des eigenen Browsers zu ändern.
- Das Projekt besteht aus einer einzigen HTML-Datei. Eine Aufteilung in ein Vite-/TypeScript-Projekt mit Unit-Tests ist nicht umgesetzt.
- Getestet wurde die Wortprüfung und die Erzeugung der Tagesschlösser in Node (Lösbarkeit, Hinweise). Das fertige Spiel wurde nicht automatisiert im Browser getestet.

## Aufbau von `index.html`

1. CSS (Design-Tokens, helles und dunkles Farbschema, Reduced-Motion-Regel)
2. Wörterbuch als Textblock
3. Spiellogik: Hinweis-Generator mit Seed, Wortprüfung, Punkte, Statistik, Ansichten, Ton (Web Audio, abschaltbar), Eingabe

Die Schlösser entstehen deterministisch: Ein Seed aus Tagesnummer und Stufe wählt pro Runde ein verstecktes Wort aus einer Wortleiter (kurz bis lang) und leitet daraus die Hinweise ab. Dadurch hat jedes Schloss garantiert eine Lösung.

## Auf GitHub veröffentlichen

Die Dateien liegen direkt im Hauptordner, `index.html` ist das fertige Spiel. Für eine Seite unter `https://<name>.github.io/<repo>/`: Repository → Settings → Pages → „Deploy from a branch“ → Branch `main`, Ordner `/ (root)`. Die leere Datei `.nojekyll` sorgt dafür, dass GitHub die Dateien unverändert ausliefert.

## Lizenz

Wörterbuch: CC0 (spielwoerter). Spielcode: nach Belieben des Projektinhabers.
