# KumarNet Captive Portal

> Das wahrscheinlich over-engineerteste Gaeste-WLAN-Portal in einer Privatwohnung.

Marc wollte eigentlich nur, dass sich Gaeste im WLAN anmelden. Herausgekommen ist ein Portal mit animiertem Partikel-Hintergrund, einem System-Info-Panel das dein Geraet komplett durchleuchtet und einer Error-Seite die dir Angst macht. Priorities.

## Was ist das hier?

Ein Captive Portal fuer OPNsense, das angezeigt wird wenn sich jemand ins Gaeste-WLAN einloggen will. Gebaut fuer den privaten Gebrauch unter Freunden - also weniger "Enterprise Security" und mehr "Marc hat zu viel Freizeit".

## Dateien

```
captive-portal/
  portal.html   - Login-Seite (die huebsche)
  error.html    - Fehler-Seite (die bedrohliche)
  logout.html   - Logout-Seite (die traurige)
```

Das war's. Drei HTML-Dateien. Kein Framework. Kein Build-Step. Kein `node_modules` Ordner der groesser ist als das Betriebssystem.

## Features

### portal.html - "Reinlassen bitte"
- Animierter Netzwerk-Partikel-Hintergrund (ja, wirklich noetig)
- Glasmorphism-Card weil wir in 2026 leben
- Rotierende witzige Untertitel und Tipps des Tages
- System-Info-Panel das dir sagt welches OS du hast (als ob du das nicht wuesstest)
- Zeigt Akku, RAM, CPU-Kerne, Netzwerk-Typ und deinen ungefaehren Aufenthaltsort in der Zeitzone an
- Der Username ist vorausgefuellt mit "guest" weil Marc nett ist

### error.html - "SICHERHEITSVERSTOSS ERKANNT"
- Rote Alarm-Aesthetik mit zitternder Card
- Blinkendes "SICHERHEITSVERSTOSS ERKANNT" Banner
- Fake Vorfall-ID, fake IP-Ermittlung, fake Standort-Triangulation
- Standorte wie "Klo, 2. OG (mit Handy)" oder "Balkon, heimlich rauchend"
- 5-Minuten Countdown bis zur "Netzwerksperre" (passiert nichts)
- Rotierende Drohungen: "Dein Spotify wechselt dauerhaft auf Volksmusik"
- Rote Partikel im Hintergrund fuer maximale Panik
- Button: "Erneut versuchen (auf eigene Gefahr)"
- Powered by der "KumarNet Cyber Defense Unit"

### logout.html - Die Verabschiedung
- Gleicher huebscher Hintergrund
- Schlichte Abmeldungsbestaetigung

## Technische Details

**Abhaengigkeiten:** Keine. Null. Nada.

**Frameworks:** Nein.

**Build-Tools:** LOL.

**Browser-Support:**
- Chrome/Edge: Alles funktioniert inkl. Battery API, Network Info, Device Memory
- Firefox: Fast alles, minus ein paar Chromium-exklusive APIs
- Safari: Das Noetigste. Apple halt.
- Internet Explorer: Nein.

**APIs die benutzt werden (wenn verfuegbar):**
- `navigator.connection` - Netzwerk-Typ & Speed
- `navigator.getBattery()` - Akku-Level
- `navigator.hardwareConcurrency` - CPU-Kerne
- `navigator.deviceMemory` - RAM (ungefaehr)
- `screen.width/height` + `devicePixelRatio` - Display-Info
- `Intl.DateTimeFormat` - Zeitzone
- User Agent Parsing - OS & Browser (Regex, keine Library, wie Maenner das machen)

Nicht verfuegbare APIs werden einfach nicht angezeigt. Keine Fehler, kein Drama (ausser auf der Error-Seite, da ist alles Drama).

## Installation auf OPNsense

1. Gehe zu **Services > Captive Portal > Zones**
2. Erstelle oder bearbeite eine Zone
3. Lade `portal.html`, `error.html` und `logout.html` hoch
4. Speichern
5. Fertig. War das so schwer?

## FAQ

**Q: Warum ist das Portal so aufwendig fuer ein Gaeste-WLAN?**
A: Weil es geht.

**Q: Loggt Marc wirklich meine Suchanfragen?**
A: Nein. Aber die Frage sagt viel ueber dich aus.

**Q: Was passiert wenn der Countdown auf 00:00 laeuft?**
A: Nichts. Absolut gar nichts.

**Q: Muss ich wirklich 3x "Es tut mir leid, Marc" sagen?**
A: Kann nicht schaden.

**Q: Warum ist der Username vorausgefuellt?**
A: Damit du nur noch das Passwort eingeben musst. Marc denkt an alles.

**Q: Wie ist das Passwort?**
A: Frag Marc. Er liebt das.

## Lizenz

Mach damit was du willst. Aber wenn du es benutzt, sag Marc Bescheid - er will angeben.

---

*Powered by Marcs Keller-Rack. Uptime seit dem letzten Stromausfall: wahrscheinlich lang.*
