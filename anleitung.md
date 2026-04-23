* * *

FY6900 / FY6800 Web Commander – Bedienungsanleitung
===================================================

Diese HTML-basierte Anwendung bietet dir eine komplette Fernsteuerung und Bedienoberfläche für den FeelElec FY6900 (und weitgehend kompatible FY6800) Signalgenerator direkt über den Webbrowser.

Die Verbindung zum Gerät erfolgt per USB. Die Anwendung läuft komplett statisch und lokal im Browser deines Rechners – **es fließen keinerlei Daten nach außen!**

> ⚠️ **Wichtiger Hinweis:** Die Möglichkeit, per USB angeschlossene Geräte direkt über den Browser anzusprechen (Web Serial API), wird derzeit **nur von Chromium-basierten Browsern** (Google Chrome, Microsoft Edge, Opera) unterstützt. Mozilla Firefox oder Apple Safari (iOS/macOS) funktionieren hierfür leider nicht!

* * *

1\. Vorbereitung & Treiber
--------------------------

Der FY6900 ist intern mit einem **CH340 USB-zu-Seriell-Wandler** ausgestattet. Bevor du das Gerät nutzen kannst, müssen gegebenenfalls die passenden Treiber auf deinem Betriebssystem installiert werden.

*   Den aktuellen Treiber findest du auf der Website des Chipherstellers WCH: 
    [CH341SER\_EXE herunterladen](https://wch-ic.com/downloads/CH341SER_EXE.html)

Nach der erfolgreichen Installation des Treibers schließe den FY6900 per USB-Kabel an den Rechner an. Um Verbindungsprobleme zu vermeiden, nutze nach Möglichkeit keinen USB-Hub, sondern einen direkten Port am Rechner. Die Datenübertragung findet mit **115200 Baud** statt.

* * *

2\. Installation & Aufruf
-------------------------

Die Web Serial API erfordert zwingend einen sicheren Kontext (`https://` oder `localhost`). Ein einfaches Doppelklicken der `index.html` von der lokalen Festplatte (`file://`) wird vom Browser aus Sicherheitsgründen blockiert!

**Du hast zwei Möglichkeiten:**

1.  **Online nutzen:** Rufe den Commander direkt über diese Webseite je nach Version der Hardware auf:

(https://katzenjens.github.io/fy6900commander/) Für alte Hardwareversionen, z.B. V. 1.3 (Keine Installation nötig).
(https://katzenjens.github.io/fy6900commander/new_version.html)  Für neuere Versionen des FY6900.
**Falls es bei der Frequenzeinstellung zu Problemen kommt, jeweils die andere Version aufrufen.**

2.  **Lokal hosten:** Klone das Repository und stelle die .html Dateien auf deinen vorhandenen Webserver oder über einen lokalen Webserver (z. B. VS Code Live Server, Python `http.server` etc.) bereit. Hinweis: Die Dateien index.html und new_version.html sind bis auf eine Zeile identisch. In dieser ist entweder "new" oder "old" vermerkt.

* * *

3\. Verbindungsaufbau
---------------------

Beim ersten Aufruf (oder nach einem Reload) der Seite ist der Status "Offline".

1.  Klicke oben rechts auf den orangenen Button **„CONNECT SERIAL“**.
2.  Es öffnet sich ein Browser-Dialog, in dem du den passenden COM-Port deines Geräts auswählst. (Wenn du dir unsicher bist, probiere die verfügbaren Ports einfach nacheinander aus).
3.  Der Rechner verbindet sich in der Regel sekundenschnell. Sobald die Verbindung steht, erscheint oben links in Grün „CONNECTED“ mitsamt dem erkannten Gerätemodell und der Firmware-Version. Der Connect-Button verwandelt sich in einen grauen **„REFRESH ALL“**\-Button.

* * *

4\. Bedienung der Hauptkanäle (CH1 & CH2)
-----------------------------------------

### Frequenzwahl

Beide Kanäle bieten eine extrem feine Auflösung bis in den Mikrohertzbereich.

*   Wähle in der Leiste ("MHz" bis "μHz") die gewünschte **Schrittweite** (Raster) aus.
*   Fahre mit der Maus über den grafischen **Drehknopf** und nutze das **Maus-Scrollrad**, um die Frequenz bequem rauf- oder runterzudrehen. Die Änderung wird _in Echtzeit_ an das Gerät gesendet!
*   Für schnellere Sprünge nutze die kleinen **\[ - \] und \[ + \] Buttons (x100)** unter dem Drehknopf, um direkt das Hundertfache des gewählten Rasters zu addieren oder zu subtrahieren.
*   Die Ausgänge schaltest du über die jeweiligen **CH ON / OFF** Tasten rechts neben dem Drehknopf live ein oder aus.

### Spannungen und Wellenform (Sicherheits-Konzept)

Die empfohlene Vorgehensweise ist: Zuerst die gewünschte Wellenform (Wave) auswählen, danach Amplitude, Offset und ggf. Parameter wie Duty-Cycle anpassen.

**Achtung:** Es sind nicht immer alle Parameter für jede Wellenform auswählbar (die Software graut nicht benötigte Felder automatisch aus). Aus Sicherheitsgründen (um angeschlossene, empfindliche Bauteile nicht durch versehentliches Scrollen und zu hohe Spannungen zu zerstören) werden diese Einstellungen **nicht in Echtzeit** übernommen!

*   Händisch veränderte Werte werden **orange markiert**.
*   Erst ein Klick auf den grünen **„SET“**\-Button überträgt die Parameter an das Gerät.
*   Ein Klick auf „REFRESH ALL“ oben rechts verwirft ungespeicherte Eingaben und liest den aktuellen Ist-Zustand aus dem Gerät neu aus.

* * *

5\. Modulation Center
---------------------

Um modulierte Signale zu erzeugen, gehe schrittweise vor:

1.  Stelle auf **CH1 die Trägerfrequenz** , weitere Parameter wie Amplitude und die Wellenform (meist Sinus) ein.
2.  Stelle auf **CH2 die Modulationsfrequenz** ein. Drücke bei beiden Kanälen auf "SET" und schalte beide Ausgänge "ON".
3.  Klappe das Akkordeon-Menü **„Modulation Center“** auf.
4.  Arbeite dich von links nach rechts durch: **1\. Mode** -\> **2\. Source** -\> **3\. Parameters**.
5.  Im Feld **4\. Modulation** klickst du auf **„START“**. Das modulierte Signal liegt nun an CH1 an, das reine Modulationssignal (z.B. für Trigger-Zwecke am Oszilloskop) an CH2.
6.  Ein Klick auf **„STOP“** beendet die Modulation und lässt CH1 wieder die unmodulierte Trägerfrequenz ausgeben.

Hinweis: Teilweise weicht die Beschriftung am Gerät von den Einstellungen in der Software ab. Es gilt aber tatsächlich das, was im Browser angezeigt wird. Komisch, aber es ist tatsächlich so. 🤣
* * *

6\. Sweep / VCO Control
-----------------------

*   Bereite CH1 vor (Amplitude, Offset, Wellenform via "SET" übernehmen).
*   Klappe das Menü **„Sweep / VCO Control“** auf und gib Modus, Objekt, Richtung sowie Start, Stop, Time und Scaling ein.
*   _Hinweis:_ Diese Sweep-Parameter werden nicht dauerhaft in der Hardware gespeichert und müssen hier in der Software eingegeben werden.
*   Drücke **„SET VALUES“**, um die Parameter in den temporären Speicher des Geräts zu laden.
*   Mit **„START / STOP“** aktivierst oder pausierst du den Sweep.
*   **„RETURN“** beendet den Sweep-Modus komplett und schaltet das Gerät in den Normalbetrieb zurück.

* * *

7\. System & ARB Wave Editor
----------------------------

Dieses Menü ist in zwei Bereiche unterteilt:

**Device Control & Memory (Links):** Steuere den Buzzer, die Uplink-Modi (Master/Slave) oder lade/speichere komplette Geräte-Setups auf den internen Speicherplätzen (Slot 0-19) des FY6900.

**Arb Waveform Editor (Rechts):** Zeichne und modifiziere eigene Arbiträr-Wellenformen mit der Maus direkt auf dem Canvas.

*   **Tools:** Erzeuge Standard-Wellen (Sinus, Rechteck, etc.), glätte handgezeichnete Kurven mit **„Smooth“** oder nulle das Signal.
*   **Dateien:** Speichere (**„SAVE“**) deine Kreation als `.fy` Datei auf deiner Festplatte, oder lade (**„LOAD“**) bestehende `.fy` Dateien in den Editor.
*   **Upload:** Wähle einen der 64 Arb-Speicherplätze aus und klicke auf **„UPLOAD“**, um die gezeichnete Welle dauerhaft in den Flash-Speicher des FY6900 zu flashen. Ein Fortschrittsbalken zeigt den Status an.

* * *

8\. Hinweise & Debugging
------------------------

Dieses Tool wurde gezielt für einen **FY6900 mit der Firmware Version 1.3** entwickelt und getestet. Es sollte auch mit neueren Versionen sowie dem Vorgänger FY6800 funktionieren, eine hundertprozentige Garantie für alle Revisionen kann jedoch nicht gegeben werden. Das Layout ist auf Desktop-Nutzung optimiert. Durch einen netten Menschen, welcher die neuere Version vom FY6900 hat, konnte ich nun auch für diese Version die passende Software bereitstellen. Im Quelltext der index.html muss nur eine Zeile angepasst werden:
 **var hwVersion = "new";**
 
 Die index.html und new_version.html sind bis auf diese Zeile identisch.

Die neue Hardwareversion hat zwei zuätzliche Wellenformen und daher 2 Speicherplätze weniger für selbsterstellte Wellenformen. Da diese ganze Herumschieberei in der Software mehr Schwierigkeiten als Sinn macht, habe ich von einer Erweiterung abgesehen. Daher sind die Positionen in der Webanzeige in der neueren Version der Hardware nicht mit denen im Display identisch.

Davon abgesehen passt die Displayanzeige am Gerät nicht immer zu der aktuellen Funktion. Das ist ein Problem bei preiswerten Geräten aus China.🙄

Solltest du auf Inkompatibilitäten stoßen oder das Protokoll untersuchen wollen, klappe das **„DEBUG TERMINAL“** ganz unten auf. Hier siehst du den rohen seriellen RX/TX-Verkehr und kannst händisch Befehle (z.B. aus der beiliegenden Befehlsreferenz) an das Gerät absetzen.

Viel Spaß mit dem „aufgebohrten“ FY6900!
