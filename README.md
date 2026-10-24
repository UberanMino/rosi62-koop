# Rosi 62 Koop – Prototyp (Raphaels Seite)

Eigenständige Spielseite in einer Datei, unabhängig von `game/src` und `build.py`.

- **Inhalt:** alle 6 Räume (01–03 von Jonathan, 04–06 von Raphael im Stand von PR #16), Texte aus `game/texte.json` (PR #25).
- **Live zu zweit:** Wer „Raum eröffnen“ klickt, ist Host und führt die verbindliche Zugliste. Der Gast tritt mit dem 5-stelligen Code bei. Zwei Wege:
  - **Direkt** per WebRTC über PeerJS (`peerjs@1.5.4` mit SRI). Scheitert zwischen vielen Netzen, weil es keinen TURN-Relay gibt.
  - **Relay** über öffentliche MQTT-Broker per WebSocket (`mqtt@5.10.1` mit SRI): HiveMQ, Mosquitto, EMQX. Der Host hört auf allen, der Gast nimmt den ersten erreichbaren. Topic = SHA-256 aus Salz und Raumcode, der Code liegt also nicht im Klartext beim Broker.
  - Der Gast versucht 5 s direkt, sonst automatisch den Relay. `?relay=1` erzwingt den Relay. Oben steht „Live verbunden (direkt)“ bzw. „(über Relay)“.
  - Rolle und unbestätigte Züge werden alle 4 s nachgesendet, weil der Relay nichts speichert. Jeder Spieler hat eine stabile ID (localStorage), der Host gibt dieselbe ID nach einem Neuladen sofort ihre Figur zurück und bestätigt mit `rolle-ok` („Du spielst …“). Relay-Gäste ohne Lebenszeichen geben ihre Figur nach 10–12 s frei. Ist die gespeicherte Figur belegt und die andere frei, wird automatisch getauscht. Der Raum ist pro Browser-Tab gespeichert (sessionStorage).
- **Rollen:** Jonathan (roter Kreis), Raphael (blaues Quadrat), „Beide“ (ein Gerät, Tab wechselt) und „Zuschauen“ (ohne geheime Hinweise). Eine Figur, die schon jemand hat, wird nicht doppelt vergeben.
- **Chat:** Eine normale Zeile im Eingabefeld geht als Chat-Nachricht an den Mitspieler. `/befehl` oder ein bekannter Befehl ohne Schrägstrich steuert die eigene Figur (`/rechts 3`, `/nimm`, `/code 0815`, `/beschuldige mieze`, `hilfe`). Spielereignisse stehen abgesetzt im selben Verlauf.
- **Ohne Verbindung:** Das Spiel läuft lokal weiter, Fortschritt bleibt im Browser (localStorage).

## Sicherheit und Grenzen

- Der Host prüft jeden Zug (`cleanZug`: nur bekannte Typen und Felder, Richtung, Schrittzahl 1–8, Code 4 Ziffern, Text max. 200 Zeichen). Ein Gast darf nur für die Figur ziehen, die seiner Verbindung zugeordnet ist, und nicht neu starten. Neu starten kann nur der Host.
- Ein fehlerhafter Zug im Speicher bricht das Nachspielen nicht ab.
- Fremdtext nur per `textContent`. Kein `innerHTML`, `eval` o. Ä.
- **Raum-Code ohne Anmeldung:** Wer den Code kennt, kann einer freien Figur beitreten. Für ein Spiel unter Freunden ok, den Code also nur dem Mitspieler schicken.
- **Datenschutz:** Der direkte Weg (WebRTC) legt die IP-Adressen beider Geräte gegenseitig offen. Beim Relay sehen nur die Broker die IPs. Öffentliche Broker sind Testdienste ohne Garantie. Google Fonts erhält beim Laden die IP.

## Wie getestet

- Script mit `node --check` geprüft.
- Spielregeln im Browser durchgespielt: Finale 06 mit geteiltem Code `0815`, falsche und richtige Beschuldigung, Abschlusskarte.
- Echte PeerJS-Verbindung zwischen Host und zweitem Peer: Gast-Zug und Chat kommen an und werden verteilt. Ein Zug für die fremde Figur, ein Neustart vom Gast und ungültige Züge werden abgewiesen. Die schon belegte Figur wird nicht vergeben.
- Live auf GitHub Pages mit zwei Browser-Tabs: Relay erzwungen (Beitritt, Rolle, Chat, Züge in beide Richtungen), direkter Weg und Neuladen des Gasts (Figur nach ca. 11 s automatisch zurück). Dabei gefunden und behoben: Broker-Mismatch (der Host hört jetzt auf allen Brokern) und verlorene Nachrichten vor dem Abo (Nachsenden).
- Noch offen: Test auf zwei echten Geräten in verschiedenen Netzen.

Hosting: https://uberanmino.github.io/rosi62-koop/ (öffentliches Repo `UberanMino/rosi62-koop`, GitHub Pages, kostenlos).
