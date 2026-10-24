# Rosi 62 Koop – Prototyp (Raphaels Seite)

Eigenständige Spielseite in einer Datei, unabhängig von `game/src` und `build.py`.

- **Inhalt:** alle 6 Räume (01–03 von Jonathan, 04–06 von Raphael im Stand von PR #16), Texte aus `game/texte.json` (PR #25).
- **Live zu zweit:** Peer-to-Peer per WebRTC über PeerJS (`unpkg.com/peerjs@1.5.4`, mit SRI-Hash, öffentlicher PeerJS-Signalisierungsserver und Standard-STUN). Wer „Raum eröffnen“ klickt, ist Host und führt die verbindliche Zugliste. Der Gast tritt mit dem 5-stelligen Code bei. Spielzüge laufen direkt zwischen den Browsern.
- **Rollen:** Jonathan (roter Kreis), Raphael (blaues Quadrat), „Beide“ (ein Gerät, Tab wechselt) und „Zuschauen“ (ohne geheime Hinweise). Eine Figur, die schon jemand hat, wird nicht doppelt vergeben.
- **Chat:** Eine normale Zeile im Eingabefeld geht als Chat-Nachricht an den Mitspieler. `/befehl` oder ein bekannter Befehl ohne Schrägstrich steuert die eigene Figur (`/rechts 3`, `/nimm`, `/code 0815`, `/beschuldige mieze`, `hilfe`). Spielereignisse stehen abgesetzt im selben Verlauf.
- **Ohne Verbindung:** Das Spiel läuft lokal weiter, Fortschritt bleibt im Browser (localStorage).

## Sicherheit und Grenzen

- Der Host prüft jeden Zug (`cleanZug`: nur bekannte Typen und Felder, Richtung, Schrittzahl 1–8, Code 4 Ziffern, Text max. 200 Zeichen). Ein Gast darf nur für die Figur ziehen, die seiner Verbindung zugeordnet ist, und nicht neu starten. Neu starten kann nur der Host.
- Ein fehlerhafter Zug im Speicher bricht das Nachspielen nicht ab.
- Fremdtext nur per `textContent`. Kein `innerHTML`, `eval` o. Ä.
- **Raum-Code ohne Anmeldung:** Wer den Code kennt, kann einer freien Figur beitreten. Für ein Spiel unter Freunden ok, den Code also nur dem Mitspieler schicken.
- **Datenschutz:** WebRTC legt die IP-Adressen beider Geräte gegenseitig offen. Google Fonts erhält beim Laden die IP.

## Wie getestet

- Script mit `node --check` geprüft.
- Spielregeln im Browser durchgespielt: Finale 06 mit geteiltem Code `0815`, falsche und richtige Beschuldigung, Abschlusskarte.
- Echte PeerJS-Verbindung zwischen Host und zweitem Peer: Gast-Zug und Chat kommen an und werden verteilt. Ein Zug für die fremde Figur, ein Neustart vom Gast und ungültige Züge werden abgewiesen. Die schon belegte Figur wird nicht vergeben.
- Noch offen: Test auf zwei echten Geräten in verschiedenen Netzen.

Hosting: keins im Repo. Die Seite war kurz über einen Make-Webhook online und ist dort offline.
