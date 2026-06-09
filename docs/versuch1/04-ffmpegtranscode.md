# Transcodierung der Videodatei auf einer virtuellen Maschine

## Schritt 1: Manifestdateierzeugung

Nachdem der Object Storage eingerichtet und das Testvideo erfolgreich hochgeladen wurde, erfolgt im nächsten Schritt die eigentliche Transcodierung der Videodatei. In AWS würde dieser Schritt durch einen verwalteten Dienst wie AWS Elemental MediaConvert übernommen werden. Da STACKIT aktuell keinen eigenen spezialisierten Transcoding-Dienst bereitstellt, wird dieser Arbeitsschritt in diesem Versuch bewusst auf einer virtuellen Maschine durchgeführt.

Die virtuelle Maschine übernimmt dabei die Rolle eines dedizierten Rechenknotens. Sie greift auf die im Object Storage abgelegte Quelldatei zu, verarbeitet diese lokal und speichert die erzeugten Ergebnisdateien anschließend wieder im Object Storage ab. Dieses Vorgehen entspricht einem typischen cloudbasierten Workflow, bei dem Speicher und Rechenleistung klar voneinander getrennt sind.

### Verbindung zur virtuellen Maschine

Zunächst wird eine Verbindung zur zuvor erstellten virtuellen Maschine hergestellt. Der Zugriff erfolgt über das Secure Shell Protokoll (SSH).

```bash
ssh -i <PFAD_ZUM_PRIVATE_KEY> ubuntu@<PUBLIC-IP-DER-VM>
```


Zur Verbindugn werden wieder ihre Keys benötigt, welche Sie sich notiert haben sollten. Geben Sie diese dort ein!


!!! question "Frage 1.4"
    Was passiert technisch, wenn der zuvor ausgeführte Befehl eingegeben wird?

    Beschreiben Sie, welche Komponenten beteiligt sind und welche Aktionen im Hintergrund ablaufen.

    Gehen Sie dabei insbesondere darauf ein, wie der Befehl mit dem Betriebssystem bzw. der Cloud-Infrastruktur interagiert.

Nach erfolgreicher Anmeldung befindet man sich auf dem Linux-System der virtuellen Maschine und kann dort weitere Software installieren und ausführen.

<div style="
  border: 2px solid #ffffff;
  padding: 14px;
  border-radius: 6px;
  margin: 14px 0;
">
  <span style="font-size:1.1em;">
    ℹ️ <strong>Hinweis:</strong>
  </span><br>
  Beim ersten Verbindungsaufbau per SSH erscheint eine Sicherheitsabfrage zum sogenannten <em>Host-Fingerprint</em>.
  Diese Abfrage dient dazu, die Identität des entfernten Servers zu überprüfen.
  Da es sich hierbei um eine neu erstellte virtuelle Maschine handelt, ist der Fingerprint dem lokalen System noch nicht bekannt.
  In diesem Fall genügt es, die Abfrage mit <code>yes</code> zu bestätigen.
  Der Fingerprint wird anschließend gespeichert, sodass diese Abfrage bei zukünftigen Verbindungen nicht erneut erscheint.
</div>

**Nach erfolgreicher Eingabe sollte folgende Ausgabe in der Powershell zu erwarten sein**

![S3 Dashboard](../assets/cmdsuccesful.jpg)

## Aktueller Stand des Versuchs

**Bis zu diesem Punkt wurden alle grundlegenden Voraussetzungen für einen cloudbasierten Transcoding-Workflow geschaffen. Die benötigte Infrastruktur ist vollständig eingerichtet und funktionsfähig.**

Zunächst wurde ein Object Storage als zentraler Speicherort für die Medieninhalte angelegt und erfolgreich getestet. Anschließend wurde eine virtuelle Maschine bereitgestellt, die als Rechenknoten für die Medienverarbeitung dient. Durch die Einrichtung von Netzwerk, Security Groups und einer öffentlichen IP-Adresse konnte der externe Zugriff auf die virtuelle Maschine ermöglicht werden.

Der erfolgreiche Verbindungsaufbau per SSH bestätigt, dass die virtuelle Maschine korrekt konfiguriert ist und aus dem Internet erreichbar ist. Damit steht nun eine lauffähige Linux-Arbeitsumgebung zur Verfügung, auf der die eigentliche Transcodierung durchgeführt werden kann.

## Ziel der nächsten Schritte

Im folgenden Abschnitt beginnt der zentrale Verarbeitungsschritt des Video-on-Demand-Workflows. Die zuvor im Object Storage abgelegte Videodatei wird auf die virtuelle Maschine übertragen und dort mithilfe einer Transcoding-Software verarbeitet.

**Konkret werden in den nächsten Schritten:**
- die Transcodierungssoftware "ffmpeg" installiert
- die benötigte Software zur Medienverarbeitung eingesetzt,
- die Quelldatei aus dem Object Storage geladen,
- eine Manifestdatei aus dem dort hinterlegten Video erzeugt
- und die transcodierten Ergebnisse wieder im Object Storage abgelegt.

Damit wird der Übergang von der reinen Infrastruktur- und Speicherbereitstellung zur eigentlichen Medienverarbeitung vollzogen, wie er auch in realen cloudbasierten Video-on-Demand-Systemen üblich ist.

### Installation der Transcoding-Software

Für die Transcodierung wird in diesem Versuch das Kommandozeilenwerkzeug **FFmpeg** eingesetzt. FFmpeg ist ein weit verbreitetes Open-Source-Werkzeug zur Verarbeitung von Audio- und Videodaten und wird sowohl in Forschung, Lehre als auch in produktiven Medien-Workflows verwendet.

Die Installation erfolgt direkt auf der virtuellen Maschine über den Paketmanager des Betriebssystems:

Bedeutet:**Geben Sie folgende Befehle in das gleiche Powershellfenster ein, wo sie die Verbindung initialisiert haben**

**Tipp:** Sie können dies kontrollieren indem vor ihrem Eingabefeld in grüner Schrift ihr Server angezeigt wird

![S3 Dashboard](../assets/cmdservergreen.jpg)


**Geben Sie bitte nun dort folgenden Befehl ein:**

```bash
sudo apt update
```

**Dieser Befehl aktualisiert die Paketlisten des Systems. Dabei wird geprüft, welche neuen Versionen von Software verfügbar sind, ohne sie direkt zu installieren.**


```bash
sudo apt install ffmpeg -y
```

**Dieser Befehl installiert das Programm ffmpeg, das für die Verarbeitung und Konvertierung von Audio- und Videodateien verwendet wird.
Die Option -y sorgt dafür, dass alle Rückfragen automatisch mit „Ja“ bestätigt werden.**

**Nach erfolgreicher Ausführung sollten sie folgende Meldung bekommen:**

![S3 Dashboard](../assets/nonono.jpg)

## Zugriff auf den Object Storage von der virtuellen Maschine

Nachdem die virtuelle Maschine vorbereitet und die Transcoding-Software installiert wurde, muss sie nun selbstständig auf den Object Storage zugreifen können. Dazu wird auf der VM die bereits bekannte S3-kompatible Schnittstelle verwendet. Die virtuelle Maschine übernimmt damit aktiv die Rolle des Transcoders und greift direkt auf die im Object Storage abgelegten Quelldateien zu.


### Nutzung der S3-kompatiblen Schnittstelle mit s3cmd

STACKIT stellt für den Object Storage keine eigenen, vollwertigen Client-Werkzeuge bereit, wie sie beispielsweise von großen Hyperscalern angeboten werden. Die Verwaltung des Object Storage erfolgt primär über die Weboberfläche des STACKIT Control Centers, in der grundlegende Aufgaben wie das Anlegen von Buckets oder das Erstellen von Zugangsdaten durchgeführt werden können.

Für den Datentransfer sowie für automatisierte Workflows stellt STACKIT eine **S3-kompatible Schnittstelle** bereit. Diese orientiert sich an der weit verbreiteten Amazon-S3-API, die sich als De-facto-Standard für objektbasierten Cloud-Speicher etabliert hat. Durch diesen Ansatz können etablierte, herstellerunabhängige Werkzeuge eingesetzt werden.

Im Rahmen dieses Versuchs wird ausschließlich das Open-Source-Werkzeug **s3cmd** verwendet.  
`s3cmd` dient hierbei als technischer Client zur Kommunikation mit der S3-kompatiblen Schnittstelle von STACKIT. Der Zugriff erfolgt explizit über den STACKIT-Endpoint, es wird **keine AWS-Infrastruktur** genutzt.

---

### Installation von s3cmd

Installieren Sie `s3cmd` über den Paketmanager des Betriebssystems:

```bash
sudo apt install s3cmd
```


**Folgende Ausgabe sollte daraus erfolgen:**

![S3 Dashboard](../assets/s3cmddone.jpg)


**Prüfen Sie die Installation bitte mit folgendem Befehl**

```bash
s3cmd --version
```

**Nun geht es an das Konfigurieren, hierfür benötigen wir den Befehl:**

```bash
s3cmd --configure
```

**Hier werden seriell der Accesskey und Secret Acceskey abgefragt, sowie Default Region Name und Default Output Format**

**Die Ausgabe sollte so aussehen:**

```bash
Enter new values or accept defaults in brackets with Enter.
Refer to user manual for detailed description of all options.

Access key and Secret key are your identifiers for Amazon S3. Leave them empty for using the env variables.
Access Key: **Ihr Public Key**
Secret Key: **Ihr Private Key**
Default Region [US]: eu01

Use "s3.amazonaws.com" for S3 Endpoint and do not modify it to the target Amazon S3.
S3 Endpoint [s3.amazonaws.com]: object.storage.eu01.onstackit.cloud
```










<div style="
  border: 2px solid #ffffff;
  padding: 14px;
  border-radius: 6px;
  margin: 14px 0;
">
  <span style="font-size:1.1em;">
    ℹ️ <strong>Hinweis:</strong>
  </span><br>
Die Keys, die Sie hier eingeben sollen, sind jene, die Sie zu Beginn in STACKIT angelegt haben.
Es wird vorausgesetzt, dass Sie sich diese sorgfältig notiert haben.
Falls dies nicht der Fall ist, können die Zugangsdaten jederzeit erneut erstellt werden.
Eine Anleitung dazu finden Sie im vorherigen Kapitel. 🙂
</div>

![S3 Dashboard](../assets/awskeys1.jpg)

### Test des Zugriffs auf den Object Storage von StackIT

Nach der erfolgreichen Konfiguration wird überprüft, ob die virtuelle Maschine auf den Object Storage zugreifen kann. Dazu wird der zuvor erstellte Bucket aufgelistet.

**Bitte geben sie folgende Befehle in die VM Console ein:**  

```bash
s3cmd ls s3://<DEINBUCKETNAME> \
  --host=object.storage.eu01.onstackit.cloud \
  --host-bucket="%(bucket).object.storage.eu01.onstackit.cloud"
```

## Download der Videodatei auf die virtuelle Maschine

Im nächsten Schritt wird die im Object Storage abgelegte Videodatei auf die virtuelle Maschine kopiert. Erst nachdem sich die Datei lokal auf der VM befindet, kann sie von der Transcoding-Software verarbeitet werden.

### Kopieren der Datei aus dem Object Storage

Der Download erfolgt mithilfe von s3cmd.
Die für den Zugriff auf den STACKIT Object Storage benötigten Endpoint-Parameter wurden zuvor im Rahmen der Konfiguration (s3cmd --configure) hinterlegt.
Der folgende Befehl muss direkt auf der virtuellen Maschine ausgeführt werden:

```bash
s3cmd get s3://<DEINBUCKETNAME>/testvideo.mp4 ./testvideo.mp4 \
  --host=object.storage.eu01.onstackit.cloud \
  --host-bucket="%(bucket).object.storage.eu01.onstackit.cloud"
```
**Folgendes Ergebis ist zu erwarten:**

![S3 Dashboard](../assets/s3cmddownload.jpg)

**Auch hier gilt wieder testen:**


```bash
ls -lh testvideo.mp4
```

## Der Transcodiervorgang
Nachdem sich die Videodatei lokal auf der virtuellen Maschine befindet und vom Bucket geholt wurde, kann nun die eigentliche Transcodierung durchgeführt werden. Ziel ist es, aus der hochauflösenden Quelldatei mehrere Distributionsformate mit unterschiedlichen Auflösungen und Bitraten zu erzeugen. Diese Formate sind für verschiedene Endgeräte und Netzwerkbedingungen optimiert.

Für die Transcodierung wird das Kommandozeilenwerkzeug **FFmpeg** verwendet. FFmpeg liest die Quelldatei ein, dekodiert sie und erzeugt neue Ausgabedateien mit den vorgegebenen Parametern.


## Transcodierung für adaptives Streaming (HLS)

Neben der Erzeugung klassischer MP4-Distributionsdateien werden in modernen
Video-on-Demand-Systemen häufig adaptive Streaming-Formate eingesetzt.
Diese bestehen aus mehreren kurzen Videosegmenten sowie sogenannten
Manifestdateien, die Informationen über verfügbare Auflösungen, Bitraten
und Segmentabfolgen enthalten.

Im folgenden Abschnitt wird gezeigt, wie mithilfe von FFmpeg aus der
hochaufgelösten Quelldatei ein HLS-kompatibles Streaming-Format inklusive
Manifestdatei erzeugt werden kann. Diese Ausgabeform eignet sich für die
Auslieferung über ein Content Delivery Network (CDN).

### Vorbereitung des Arbeitsverzeichnisses

Zunächst wird auf der virtuellen Maschine ein separates Ausgabeverzeichnis
für die HLS-Dateien angelegt:

```bash
mkdir hls_output
```

**Erstellung der Manifest- und Segmentdateien**

Die Transcodierung erfolgt erneut mithilfe von FFmpeg. Im Gegensatz zur
klassischen MP4-Ausgabe wird hierbei das HLS-Ausgabeformat verwendet.
FFmpeg erzeugt dabei automatisch eine Manifestdatei sowie mehrere
Videosegmente.
`
**Geben Sie folgenden Command in das von ihnen gerade erstellte Konsolenverzeichnis ein:**

```bash
ffmpeg -i testvideo.mp4 \
  -map 0:v:0 \
  -map 0:v:0 \
  -map 0:v:0 \
  -c:v libx264 -profile:v main -crf 20 \
  -filter:v:0 scale=854:480  -b:v:0 1200k -maxrate:v:0 1300k -bufsize:v:0 2600k \
  -filter:v:1 scale=1280:720 -b:v:1 3000k -maxrate:v:1 3300k -bufsize:v:1 6600k \
  -filter:v:2 scale=1920:1080 -b:v:2 5500k -maxrate:v:2 6000k -bufsize:v:2 12000k \
  -var_stream_map "v:0,name:480p v:1,name:720p v:2,name:1080p" \
  -f hls -hls_time 4 -hls_playlist_type vod \
  -hls_flags independent_segments \
  -master_pl_name master.m3u8 \
  hls_output/stream_%v.m3u8
```

**Was genau bewirkt der Befehl?**
Der ausgeführte FFmpeg-Befehl erzeugt aus der Eingabedatei testvideo.mp4 mehrere Versionen desselben Videos mit unterschiedlichen Qualitätsstufen. Diese Gesamtheit der Varianten wird als HLS Bitrate-Ladder bezeichnet.

Im vorliegenden Befehl wird der Videostream der Quelldatei dreimal verwendet **(-map 0:v:0)**. Jede dieser Kopien wird anschließend separat verarbeitet: Eine Variante wird auf **480p** skaliert, eine auf **720p** und eine auf **1080p.** Gleichzeitig werden für jede Auflösung passende Zielbitraten definiert, sodass jede Version eine eigene, klar abgegrenzte Qualitätsstufe darstellt.

Mithilfe der Option **-var_stream_map** werden diese Varianten logisch zusammengefasst. FFmpeg erzeugt daraus automatisch mehrere zentrale Master-Playlists **(master.m3u8)** sowie jeweils eigene Playlists und Segmentdateien pro Qualitätsstufe.

Ein HLS-Player kann anhand dieser Struktur während der Wiedergabe zwischen den erzeugten Varianten wechseln und so die Videoqualität an die aktuelle Netzwerkverbindung anpassen. Der Transcoding-Schritt bildet damit die technische Grundlage für adaptives Streaming im Video-on-Demand-Workflow.

Die folgenden Optionen definieren für jede erzeugte Rendition sowohl die Zielauflösung als auch das Bitratenverhalten des Videos:

```bash
-filter:v:0 scale=854:480  -b:v:0 1200k -maxrate:v:0 1300k -bufsize:v:0 2600k
-filter:v:1 scale=1280:720 -b:v:1 3000k -maxrate:v:1 3300k -bufsize:v:1 6600k
-filter:v:2 scale=1920:1080 -b:v:2 5500k -maxrate:v:2 6000k -bufsize:v:2 12000k
```

Für jede Qualitätsstufe wird das Video zunächst mit scale auf eine feste Zielauflösung umgerechnet. Dadurch entstehen drei klar voneinander getrennte Versionen des Inhalts, die für unterschiedliche Endgeräte und Bildschirmgrößen geeignet sind.

Die Option -b:v legt die angestrebte durchschnittliche Videobitrate der jeweiligen Rendition fest. Niedrigere Auflösungen erhalten bewusst geringere Bitraten, während höhere Auflösungen entsprechend mehr Bandbreite nutzen dürfen. Dadurch bleibt das Verhältnis zwischen Bildqualität und Datenrate ausgewogen.

Mit -maxrate wird eine Obergrenze für kurzfristige Bitratenspitzen definiert. Diese Begrenzung ist insbesondere für Streaming relevant, da starke Bitratenschwankungen zu Pufferproblemen beim Client führen können.

Der Parameter -bufsize beschreibt die Größe des Rate-Control-Puffers und bestimmt, über welchen Zeitraum Bitratenschwankungen ausgeglichen werden dürfen. In Kombination mit -b:v und -maxrate sorgt dieser Mechanismus für ein gleichmäßiges und vorhersagbares Bitratenverhalten der HLS-Segmente.

**Sie sollten sowas in etwa sehen:**

![S3 Dashboard](../assets/hlsladder.jpg)

**Danach kann geprüft werden ob die Manifestdateien wirklich angelegt worden sind:**

```bash
ls -lh hls_output
```
![S3 Dashboard](../assets/moremasterplaylists.jpg)

Anzeigen der Manifestdatei

Die Manifestdatei ist eine reine Textdatei und kann mit einfachen
Kommandozeilenwerkzeugen betrachtet werden.
Dazu wird der Inhalt der Datei master.m3u8 mit dem Befehl cat ausgegeben:


```bash
cat hls_output/master.m3u8
```


**Die Ausgabe sieht danach dann so aus:**

![S3 Dashboard](../assets/catinfo1.jpg)

!!! question "Frage 1.5"
    Analysieren Sie die Ausgabe der Datei <code>master.m3u8</code>, die mit dem
    Befehl <code>cat</code> angezeigt wurde.

    Erläutern Sie, welche Informationen in der Manifestdatei enthalten sind und
    welche Bedeutung die einzelnen Einträge (z. B. <code>#EXT</code>-Tags und
    Segmentreferenzen) für die Wiedergabe des Videos haben.

    Gehen Sie dabei insbesondere darauf ein, wie das Manifest den Aufbau des
    Videostreams beschreibt und warum die eigentlichen Mediendaten nicht direkt
    in der Manifestdatei enthalten sind.


## Einspeisung der Manifest- und Segmentdateien in den STACKIT Object Storage

Nach der Untersuchung der Manifestdatei werden die erzeugten HLS-Dateien wieder
in den Object Storage übertragen.
Dazu wird das gesamte Verzeichnis hls_output in einen Unterordner des zuvor
erstellten Buckets kopiert.

Upload der HLS-Dateien

```bash
s3cmd sync hls_output/ s3://<DEINBUCKETNAME>/hls/ \
  --host=object.storage.eu01.onstackit.cloud \
  --host-bucket="%(bucket).object.storage.eu01.onstackit.cloud"
```

Dabei werden sowohl die Manifestdatei (master.m3u8) als auch alle zugehörigen
Segmentdateien (.ts) übertragen.

# Überprüfung des Uploads

Um zu prüfen, ob die Dateien erfolgreich im Object Storage abgelegt wurden,
wird der Inhalt des Zielverzeichnisses aufgelistet:

```bash
s3cmd ls s3://<DEINBUCKETNAME>/hls/ \
  --host=object.storage.eu01.onstackit.cloud \
  --host-bucket="%(bucket).object.storage.eu01.onstackit.cloud"
```

In der Ausgabe sollten nun sowohl die Manifestdatei als auch mehrere
Segmentdateien angezeigt werden.



### Übersicht der Zielparameter

In diesem Versuch werden drei Ausgabeformate erzeugt:

- Full HD (1080p) für leistungsfähige Endgeräte  
- HD (720p) für mittlere Bandbreiten  
- SD (480p) für mobile oder eingeschränkte Netzwerkbedingungen  

### Transcodierung in 1080p

Zunächst wird eine Version mit einer Auflösung von 1920×1080 Pixeln erzeugt.


**Geben Sie dafür bitte folgenden Command in die VM CLI ein:**

```bash
ffmpeg -i testvideo.mp4 \
-vf scale=1920:1080 \
-c:v libx264 -b:v 5M \
-c:a aac -b:a 192k \
testvideo_1080p.mp4
```
**Dabei werden folgende Einstellungen verwendet:**

Skalierung auf 1920×1080 Pixel

H.264-Video-Codec (libx264)

Videobitrate von 5 Mbit/s

AAC-Audio mit 192 kbit/s


**Nach Abschluss der Transcodierung kann überprüft werden, ob alle Dateien erfolgreich erstellt wurden:**

```bash
ls -lh testvideo_*.mp4
```
---



!!! question "Frage 1.6"
  Fertigen Sie einen Screenshot der abschließenden <em>libx264</em>-Ausgabe an, die nach dem Transcodierungsvorgang in der Konsole angezeigt wird.
  Interpretieren Sie anhand dieser Ausgabe, wie der Encoder die Videodaten verarbeitet hat.
  Gehen Sie dabei insbesondere auf die Verteilung von <em>I-, P- und B-Frames</em>, die angezeigten
  <em>QP-Werte</em> sowie die resultierende durchschnittliche Bitrate ein und erläutern Sie,
  was diese Informationen über Qualität, Komplexität und Effizienz der Transcodierung aussagen.


**Wiederhholen SIe diesen Befehl bitte auch für 720p und 480p. Nehmen Sie sich den obrigen Befehl für die 1080p COdierung als Hilfe**


## Transcodierten Dateien wieder in den Bucket einfügen
Nachdem die Videodatei erfolgreich in mehrere Distributionsformate transcodiert wurde, müssen die erzeugten Ausgabedateien wieder im zentralen Cloud-Speicher abgelegt werden. Dieser Schritt ist notwendig, damit die transcodierten Inhalte unabhängig von der virtuellen Maschine verfügbar sind und in einem weiteren Schritt beispielsweise über ein Content Delivery Network (CDN) ausgeliefert oder weiterverarbeitet werden können.

Der Upload der Dateien erfolgt ausgehend von der virtuellen Maschine zurück in den STACKIT Object Storage. Damit wird der vollständige Verarbeitungszyklus eines cloudbasierten Video-on-Demand-Workflows abgeschlossen: von der Speicherung der Quelldatei über die Verarbeitung auf einer Recheninstanz bis hin zur erneuten Ablage der Ergebnisse im Objektspeicher.

**Zunächst wird geprüft, ob alle transcodierten Dateien lokal auf der virtuellen Maschine vorhanden sind:**

```bash
ls -lh testvideo_*p.mp4
```

*Es sollten alle erzeugten Versionen (z. B. 1080p, 720p, 480p) angezeigt werden.*


**Die transcodierten Dateien werden nun nacheinander in den zuvor erstellten Bucket hochgeladen:**

```bash
s3cmd put testvideo_1080p.mp4 s3://<DEINBUCKETNAME>/ \
  --host=object.storage.eu01.onstackit.cloud \
  --host-bucket="%(bucket).object.storage.eu01.onstackit.cloud"

s3cmd put testvideo_720p.mp4 s3://<DEINBUCKETNAME>/ \
  --host=object.storage.eu01.onstackit.cloud \
  --host-bucket="%(bucket).object.storage.eu01.onstackit.cloud"

s3cmd put testvideo_480p.mp4 s3://<DEINBUCKETNAME>/ \
  --host=object.storage.eu01.onstackit.cloud \
  --host-bucket="%(bucket).object.storage.eu01.onstackit.cloud"
```

!!! question "Frage 1.7"
  <span style="font-size:1.1em;">
     <strong>Fleißaufgabe:</strong>
  </span><br>
  Recherchieren Sie, mit welchem <em>Syntax</em> mehrere transcodierte Dateien gleichzeitig
  aus einem Verzeichnis in den Object Storage hochgeladen werden können.
  Erläutern Sie anhand des verwendeten Befehls, wie mithilfe von
  <code>--include</code> und <code>--exclude</code> gezielt nur bestimmte Dateien
  (z. B. alle transcodierten Versionen eines Videos) übertragen werden.


**Das Ergebnis sollte so aussehen:**

![S3 Dashboard](../assets/bucketuploaded.jpg)


**Nach Abschluss des Uploads wird überprüft, ob die Dateien erfolgreich im Object Storage abgelegt wurden:**

```bash
scp ubuntu@192.214.178.41:~/transcoded_download/testvideo_1080p.mp4 "$env:USERPROFILE\Desktop\MediaInfo_<NAME>"
```


**Wiederholen Sie diesen Vorgang auch für die anderen transcodierten Videos. Tragen Sie diese Commands auch wieder in die Arbeitsmappe ein**

![S3 Dashboard](../assets/Versuch1/uploadedintobucket.jpg)

## Download der transcodierten Dateien und Analyse mit MediaInfo

Nach Abschluss der Transcodierung befinden sich die erzeugten Distributionsdateien im Object Storage.  
Im nächsten Schritt werden diese Dateien lokal auf den eigenen Rechner heruntergeladen, um sie anschließend mit dem Tool *MediaInfo* zu analysieren.

### Lokalen Ordner anlegen

Damit die Dateien übersichtlich abgelegt werden, wird auf dem eigenen Rechner ein neuer Ordner mit dem eigenen Namen erstellt.  
In diesem Ordner werden später sowohl die Videodateien als auch die Analyseergebnisse gespeichert.

**Legen Sie diesen Ordner bitte auf Ihren Desktop an und nennen Sie ihn Mediainfo_vornamenachname also Mediainfo_maxmustermann**

**Nach Abschluss des Uploads wird überprüft, ob die Dateien erfolgreich im Object Storage abgelegt wurden:**

```bash
scp ubuntu@<IP des Servers>:~/transcoded_download/testvideo_1080p.mp4 "$env:USERPROFILE\Desktop\MediaInfo_<NAME>"
```


**Wiederholen Sie diesen Vorgang auch für die anderen transcodierten Videos. Tragen Sie diese Commands auch wieder in die Arbeitsmappe ein**


## Analyse der transcodierten Dateien mit MediaInfo

Nach dem Download aus der virtuellen Maschine liegen die transcodierten Videodateien lokal auf dem eigenen Rechner, z. B. auf dem Desktop.  
Im nächsten Schritt werden diese Dateien mit dem Analysewerkzeug *MediaInfo* untersucht.

![S3 Dashboard](../assets/Versuch1/mediainfo.jpg)

### Vorbereitung

Zuerst muss sichergestellt werden, dass *MediaInfo* installiert ist.  
Das Programm kann von der offiziellen Webseite heruntergeladen und installiert werden.

https://mediaarea.net/de/MediaInfo/Download

---

### Analyse über die grafische Oberfläche

1. MediaInfo starten  
2. Die gewünschte Videodatei per Drag-and-Drop in das MediaInfo-Fenster ziehen  
   oder über **Datei → Öffnen** auswählen  
3. Die technischen Informationen der Datei werden automatisch angezeigt

MediaInfo zeigt unter anderem folgende Parameter an:
- Auflösung
- Video-Bitrate
- Audio-Bitrate
- Codec
- Framerate
- Containerformat

![S3 Dashboard](../assets/Versuch1/mediainfoui.jpg)


**Darunter sollten Sie jetzt Werte angezeigt bekommen wie bspw: Format, Formatprofil,...**
**Fertigen Sie bitte einen Screenshot der verschiedenen transcodierten Versionen an und betten Sie diese in ihre Abgabemappe ein**


!!! question "Frage 1.8"
  **Nun ist ihre kreativität gefragt...**
  Transcodieren Sie die Datei <code>testvideo.mp4</code> erneut mit <em>FFmpeg</em> und nehmen Sie dabei eigenständig Anpassungen an den Transcodierungsparametern vor.<br><br>

  Verändern Sie mindestens zwei der folgenden Punkte:
  <ul>
    <li>Video-Bitrate</li>
    <li>Audio-Bitrate</li>
    <li>Framerate</li>
  </ul>


  Der benötigte FFmpeg-Befehl kann eigenständig aus der offiziellen Dokumentation oder durch Recherche im Internet ermittelt werden.<br><br>

  Analysieren Sie die erzeugte Ausgabedatei anschließend mit <em>MediaInfo</em> und dokumentieren Sie:
  <ul>
    <li>die gewählten Parameter</li>
    <li>den verwendeten FFmpeg-Befehl</li>
    <li>die Unterschiede zur vorherigen Transcodierung</li>
  </ul>







