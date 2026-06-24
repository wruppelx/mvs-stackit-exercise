# Kurzinfo

In dieser Teilaufgabe sollen die transcodierten Dateien und die CDN-Auslieferung weiter analysiert werden

## Voraussetzungen

- Eine transcodierte Videodatei liegt im STACKIT Bucket
(z. B. stream_1080p0.ts)

- Die Datei ist über Fastly erreichbar
(z. B. https://cdn2-[HDS-Nutzername].global.ssl.fastly.net/hls_output/stream_1080p0.ts)

- Zugriff auf eine Linux-VM  mit curl und ffmpeg/ffprobe

## Ablaufplan


Öffnen Sie erneut mit `ssh`die Verbindung zu Ihrer STACKIT-VM.

### CDN-Antwort abrufen

Ziel dieses Schrittes:

- Prüfen ob die Datei erreichbar ist

- Die CDN-Antwort analysieren


**Führen Sie bitte diesen Befehl aus:**

```bash
curl -I "https://cdn2-[HDS-Nutzername].global.ssl.fastly.net/hls_output/stream_1080p0.ts"
```

**Sie sollten jetzt eine Auflistung sehen:**

!!! question "Aufgabe 3.1"
    Dokumentieren Sie die Ausgabe von curl in Ihrer Ausarbeitung und interpretieren Sie diese.

### Analyse der Streaming-Dateien 

Zur Analyse der ersten Streaming-Datei geben sie  folgenden Befehl ein:**

```bash
ffprobe -v error \
-show_format \
-show_streams \
"https://cdn2-[HDS-Nutzername].global.ssl.fastly.net/hls_output/stream_1080p0.ts"
```

**Die Ausgabe sieht etwa so aus:**
![ObjectSTorage](../assets/Versuch3/stream.jpg)

Wiederholen Sie daen Befehl für die folgenden weiteren Dateien :

stream_720p0.ts

stream_480p0.ts

!!! question "Aufgabe 3.2: Vergleich der Transcoding-Ergebnisse"
    Analysieren Sie die Ausgaben von <code>ffprobe</code> für die folgenden Dateien:
    <ul>
      <li><code>stream_1080p0.ts</code></li>
      <li><code>stream_720p0.ts</code></li>
      <li><code>stream_480p0.ts</code></li>
    </ul>

    Gehen Sie dabei insbesondere auf folgende Punkte ein:
    <ul>
      <li>Welche technischen Parameter unterscheiden sich zwischen den Dateien?</li>
      <li>Welche Parameter sind bei allen Versionen identisch?</li>
      <li>Wie verändert sich die Bitrate im Verhältnis zur Auflösung?</li>
      <li>Welche Auswirkungen haben diese Unterschiede auf Bandbreite und Speicherbedarf?</li>
    </ul>

