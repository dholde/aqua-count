# Aqua Count Feedback & Support

## Einschätzung von existierenden Geräten

Ich habe noch zwei weitere potentielle Lösungen gefunden. Aber keine der Lösungen scheint gut geeignet zu sein.

| Kriterium                          | [IKEA BERGVATTNET](https://www.ikea.com/de/de/p/bergvattnet-wasserzaehler-fuer-dusche-20568698/) | [Flume Water](https://flumewater.com/) | [WaterFlowKit](https://waterflowkit.nl/en) |
| ---------------------------------- | :--------------: | :-----------: | :-----------: |
| Wassermenge messen                 | ✅               | ✅            | ✅            |
| Wassertemperatur messen            | ✅               | ❌            | ✅            |
| Zugriff auf Messdaten möglich      | ❌               | ✅ ¹          | ✅            |
| Kann an der Duschleitung angebracht werden | ✅         | ❌ ²          | ❌ ³           |
| Darf kommerziell genutzt werden    | ❌               | ❌ ⁴          | ❓⁵            |

¹ Flume bietet eine *Personal API* (REST, cloud-to-cloud). Dokumentation: [Flume Personal API – Help Center](https://help.flumewater.com/en/articles/3108017-flume-personal-api)

² Flume wird am Hauswasserzähler montiert und misst den gesamten Wasserverbrauch des Haushalts, nicht einzelner Leitungen. [Flume – How It Works](https://flumewater.com/how-it-works/)

³ WaterFlowKit wird in die Wasserleitung (Rohrinstallation) eingebaut und erfordert Klempnerarbeiten. Kein einfaches Anstecken am Duschschlauch möglich. [WaterFlowKit – Installation](https://waterflowkit.nl/en)

⁴ Die Flume Website sagt es ist eine "Personal API" was eine kommerzielle Nutzung wahrscheinlich ausschließt.

⁵ WaterFlowKit scheint verfügbar zu sein für die kommerzielle Nutzung. Sollte aber genauer abgeklärt werden. Für den Pitch sollte das aber völlig ok sein das Gerät zu nennen.



## Prototyp Realisierung

Der Prototyp besteht aus drei Teilen: einem Sensor an der Dusche, einem Server im Internet und einer App für die Nutzer.

### Einkaufsliste

| Bauteil | Beschreibung | ca. Preis | Link |
| ------- | ------------ | --------: | ---- |
| Raspberry Pi 4 Model B (2 GB) | Der kleine Computer, der alles steuert | 58 € | [Reichelt](https://www.reichelt.de/de/de/raspberry-pi-4-b-4x-1-5-ghz-2-gb-ram-wlan-bt-rasp-pi-4-b-2gb-p259919.html) |
| 7" Touchscreen Display | Bildschirm zum Antippen – zeigt die Nutzerauswahl und Live-Daten | 62 € | [Welectron](https://www.welectron.com/Offizielles-Raspberry-Pi-Touchscreen-Display-7) |
| Gehäuse für Pi + Display | Schutzhülle, die Pi und Bildschirm zusammenhält | 13 € | [Reichelt](https://www.reichelt.de/de/de/shop/produkt/gehaeuse_fuer_raspberry_pi_4_7_touch-display-268976) |
| YF-B7 Durchflusssensor (G1/2") | Misst Wassermenge und Temperatur – wird zwischen Duschmischer und Schlauch geschraubt | 10 € | [SmartHomeShop](https://smarthomeshop.io/en/products/water-flow-sensor-yf-b7-g1-2-dn15-with-temperature-sensor) |
| USB-C Netzteil (5V, 3A) | Stromversorgung für den Pi | 10 € | [Reichelt](https://www.reichelt.de/de/de/raspberry-pi-netzteil-5-1-v-3-0-a-usb-type-c-eu-stecker-s-rpi-ps-15w-bk-eu-p260010.html) |
| microSD-Karte (32 GB) | Speicherkarte für das Betriebssystem des Pi | 8 € | [Reichelt](https://www.reichelt.de/de/de/microsdhc-speicherkarte-32gb-sandisk-ultra-sdsqua4-032g-gn6-sdsqua4-032g-gn6ma-p268142.html) |
| Jumperkabel (Female-Female) | Verbindungskabel zwischen Sensor und Pi | 5 € | [Reichelt](https://www.reichelt.de/de/de/40-poliges-gpio-kabel-fuer-raspberry-pi-20-cm-ff-rpi-jw-20-ff40-p363846.html) |
| **Gesamt** | | **~166 €** | |

### Teil 1: Der Sensor an der Dusche

> *Was macht das?*
> Ein kleiner Sensor wird zwischen den Duschmischer und den Duschschlauch geschraubt (wie ein Zwischenstück). Er misst, wie viel Wasser durchfließt und wie warm es ist. Ein kleiner Computer (Raspberry Pi) mit Touchscreen hängt daneben an der Wand. Vor dem Duschen tippt man seinen Namen an. Während dem Duschen sieht man live den Verbrauch.

**Aufbau:**
- Der **YF-B7-Sensor** wird am G1/2"-Gewinde zwischen Duschmischer und Schlauch eingeschraubt – kein Klempner nötig
- Der Sensor ist per Kabel (~1 m) mit dem **Raspberry Pi** verbunden
- Der Pi liest die Daten aus und zeigt sie auf dem **Touchscreen** an
- Der Pi ist per **WLAN** mit dem Internet verbunden

**Was auf dem Bildschirm passiert:**
1. Startbildschirm: Nutzer wählt seinen Namen aus
2. Während der Dusche: Live-Anzeige von Litern, Temperatur und Dauer
3. Nach der Dusche: Zusammenfassung der Session

### Teil 2: Der Server im Internet

> *Was macht das?*
> Ein Server im Internet sammelt die Duschdaten aller Nutzer. Er speichert, wer wann wie lange und mit wie viel Wasser geduscht hat. Er berechnet Statistiken und stellt die Daten für die App bereit.

**Was der Server macht:**
- **Empfängt** die Dusch-Sessions vom Raspberry Pi (verschlüsselt per HTTPS)
- **Speichert** alle Daten in einer Datenbank (wer, wann, wie viele Liter, welche Temperatur, wie lange)
- **Berechnet** Statistiken: Durchschnitt pro Person, Trends über Zeit, Ranking
- **Stellt Daten bereit** für die App über eine Schnittstelle (REST API)

**Technisch:**
- Ein Server ist ein Computer, der über das Internet erreichbar ist
- Der Server enthält die Logik der Applikation wie zum Beispiel das Berechnen von Statistiken, die Verbindung zur Datenbank zum Speichern und wiedergeben von Daten (z.B. Speicherung von Prfilen in der Datenbank, Verbrauch pro Profil)
- Server und Datenbank kann bei verschiedenen Anbietern "gemietet" werden (z.B. Amazon Web Services, Microsoft Azure, Google Cloud Platform, Hetzner, Supabase, ...)


### Teil 3: Die App für die Nutzer

> *Was macht das?*
> Jeder Nutzer kann auf seinem Handy sehen, wie viel Wasser er verbraucht hat, wie er sich über die Zeit verbessert und wie er im Vergleich zu den anderen abschneidet.

**Was die App zeigt (ihr wisst hier am Besten welche Funktionen wichtig sind):**
- **Mein Verbrauch**: Letzte Dusche, Tagesdurchschnitt, Wochenverlauf
- **Mein Trend**: Werde ich besser oder schlechter über die Zeit?
- **Ranking**: Wer in der WG / im Haushalt duscht am sparsamsten?
- **Temperatur**: Durchschnittliche Wassertemperatur pro Dusche

**Umsetzung als Prototyp:**
- Progressive Web App (PWA) – funktioniert auf jedem Handy im Browser, kein App-Store nötig
- Später bei Bedarf als native App (React Native oder Flutter) möglich

### Datenfluss

Hier ist der für die Präsentation geeignete Teil, da er einen Überblick zeigt, wie das komplette Setup funktionieren könnte.

```
Dusche                         Internet                    Handy
──────                         ────────                    ─────

┌─────────┐    Kabel    ┌──────────┐    WLAN/HTTPS    ┌──────────┐    HTTPS    ┌─────────┐
│ YF-B7   │ ─────────── │ Pi +     │ ────────────────── │  Server  │ ────────── │  App    │
│ Sensor  │             │ Display  │                   │ (Cloud)  │            │ (Handy) │
└─────────┘             └──────────┘                   └──────────┘            └─────────┘
 misst Wasser            zeigt Live-Daten               speichert &             zeigt Stats
 & Temperatur            & sendet Sessions              berechnet               & Ranking
```


## Kosteneinschätzung für die Entwicklung

Die Kosten unten beziehen sich auf die **Softwareentwicklung**, nicht auf die Hardware (siehe Einkaufsliste oben). Geschätzt wird mit einem Stundensatz von ~100 €/h (typisch für einen erfahrenen Freelance-Entwickler in Deutschland).

Die günstigste und meiner Meinung nach beste Alternative: jemanden ins Team holen, der entwickeln kann – z. B. Mitschüler mit Programmiererfahrung.

Vorteile:
- **Deutlich günstiger** als ein externer Freelancer oder eine Agentur
- **Gerade bei Variante B (Web-App)** reichen überschaubare Vorkenntnisse aus, um einen funktionierenden Prototyp zu bauen
- **Professionelle Hilfe nur punktuell nötig** – z. B. für spezifische technische Hürden, nicht für das gesamte Projekt
- **Höhere Motivation** – wer Teil des Teams ist, hat mehr Antrieb, ein gutes Ergebnis abzuliefern, als ein externer Dienstleister
- **Flexibel bei Änderungen** – gerade am Anfang braucht man viele Iterationen, bis klar ist, wie das endgültige Produkt aussehen soll. Mit jemandem im Team kann man beliebig oft anpassen, ohne dass jede Änderung zusätzliche Kosten verursacht (außer Zeit)


### Variante A: Mit nativen Apps (iOS + Android)

| Bereich | Was wird gemacht | Aufwand | Kosten |
| ------- | ---------------- | ------: | -----: |
| Pi-Software | Sensor auslesen, Touchscreen-Oberfläche, Nutzerauswahl, Session-Logik, Datenversand | 40–60 h | 4.000–6.000 € |
| Server-Backend | API-Schnittstelle, Datenbank, Statistik-Berechnung, Ranking, Deployment | 40–60 h | 4.000–6.000 € |
| Native Apps (React Native) | App für iOS + Android aus einer Codebasis: Dashboard, Verlauf, Ranking, Design | 80–120 h | 8.000–12.000 € |
| **Gesamt** | | **160–240 h** | **16.000–24.000 €** |

### Variante B: Mit Web-App statt nativer Apps (günstiger)

Statt nativer Apps kann eine Progressive Web App (PWA) gebaut werden. Diese läuft im Browser auf jedem Handy und muss nicht über den App Store installiert werden. Spart deutlich Kosten, sieht aber fast genauso aus wie eine echte App.

| Bereich | Was wird gemacht | Aufwand | Kosten |
| ------- | ---------------- | ------: | -----: |
| Pi-Software | (identisch zu Variante A) | 40–60 h | 4.000–6.000 € |
| Server-Backend | (identisch zu Variante A) | 40–60 h | 4.000–6.000 € |
| Web-App (PWA) | Dashboard, Verlauf, Ranking – im Browser, kein App Store nötig | 30–50 h | 3.000–5.000 € |
| **Gesamt** | | **110–170 h** | **11.000–17.000 €** |

### Laufende Kosten

| Posten | Kosten |
| ------ | -----: |
| Cloud-Server (z. B. Hetzner) | ~5–10 €/Monat |
| Domain (z. B. aqua-count.de) | ~10–15 €/Jahr |
| Apple Developer Account (nur bei nativer iOS-App) | 99 €/Jahr |
| Google Play Developer (nur bei nativer Android-App) | 25 € einmalig |
| Wartung Pi-Software (Updates, Bugfixes, Sensorkompatibilität) | ~2.000–4.000 €/Jahr |
| Wartung Server-Backend (Sicherheitsupdates, Monitoring, DB-Pflege) | ~2.000–4.000 €/Jahr |
| Wartung Native Apps (OS-Updates, App-Store-Anforderungen, Bugfixes) | ~3.000–6.000 €/Jahr |
| Wartung Web-App / PWA (Browser-Kompatibilität, Bugfixes) | ~1.500–3.000 €/Jahr |

### Kosten für Online-Zahlungen

Wenn Nutzer für die App bezahlen sollen, fallen pro Transaktion Gebühren an. Welcher Zahlungsanbieter genutzt wird, hängt davon ab, wo die Zahlung stattfindet:

| Plattform | Anbieter | Gebühr pro Zahlung |
| --------- | -------- | -----------------: |
| Web-App / PWA (Zahlung im Browser) | [Stripe](https://stripe.com/de/pricing) | 1,5 % + 0,25 € (EU-Karten) |
| iOS (In-App-Kauf) | Apple App Store | 15 % (bis 1 Mio. $/Jahr), danach 30 % |
| Android (In-App-Kauf) | Google Play Store | 15 % (bis 1 Mio. $/Jahr), danach 30 % |

Auch hier ein klarer Vorteil der **Web-App (Variante B)**: Mit Stripe zahlt man nur ~1,5 % Gebühren statt 15–30 % an Apple oder Google.

### Empfehlung

Für den **Prototyp** ist Variante B (Web-App) die sinnvollere Wahl: schneller fertig, günstiger, und es lässt sich alles testen. Native Apps lohnen sich erst, wenn das Produkt validiert ist und z. B. Push-Benachrichtigungen oder Offline-Funktionen wirklich gebraucht werden.




