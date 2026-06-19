# STACKIT Object Storage

Zuerst wird der Speicherort für die transcodierten Dateien angelegt.  
Dazu wird der **STACKIT Object Storage** verwendet, ein S3-kompatibler Objektspeicher.

**Der Zugriff erfolgt über das STACKIT Portal im Browser. Auf der rechten Reiterseite. Hier findet man unter dem Reiter Computing den Unterpunkt "Object storage"**

![ObjectSTorage](../assets/Versuch1/enableobjectstorage.jpg)

**Hier muss der Objektspeicher manuell aktiviert werden. Hierfür muss nur der vorhandene Button "Objektspeicher aktivieren" geklickt werden.**

**Nun bitte zu dem Unterreiter Buckets navigieren**

![ObjectSTorage](../assets/Versuch1/Bucket-select.jpg)


!!! question "Frage 1.1"
    Wie erklären Sie sich, dass auch der Cloud-Anbieter STACKIT das S3-Protokoll für seinen Object Storage nutzt?

    Finden Sie die Spezifikation des S3-Protokolls und recherchieren Sie die Lizenzbedingungen.
  
**Klicken Sie auf das Feld "+ Bucket erstellen"**

![S3 Dashboard](../assets/Versuch1/testme.jpg)

Die Namensvergabe soll nach einem einheitlichen Schema erfolgen:  

<span style="color:orange; font-weight:bold; font-size:1.2em;">`bucket-<HDS-Nutzername>`  Beispiel: `bucket-musterax`  </span><br>


!!! info
  
  Notieren Sie sich alle im Projekt verwendeten <strong>Zugangsdaten</strong>, 
  <strong>Namen</strong> und <strong>Bezeichnungen</strong> (z. B. Bucket-Namen, 
  Benutzernamen oder Projektkennungen).  
  Diese Informationen werden benötigt, um den Versuch später auch 
  <strong>außerhalb der Präsenzveranstaltung</strong> (z. B. von zu Hause aus) 
  weiterzuführen.


## Vorbereitung: Zugangsdaten (Keys) im STACKIT Control Center erstellen

Bevor Dateien hochgeladen werden können, müssen **Zugangsdaten (Keys)** für den Object Storage angelegt werden.  
Diese werden später in der Kommandozeile verwendet.


1. Navigieren Sie zu: Object Storage → Credentials & Groups

![S3 Dashboard](../assets/Versuch1/credngru.jpg)

2. In der Übersicht werden die vorhandenen Credential Groups angezeigt. Standardmäßig existiert bereits eine Gruppe mit dem Namen default.

3. Öffnen Sie die Gruppe default, indem Sie diese anklicken.

4. Wechseln Sie innerhalb der Gruppe in den Reiter Credentials.

![S3 Dashboard](../assets/Versuch1/crediz.jpg)


5. Klicken Sie auf Create Credentials und erstellen Sie neue Zugangsdaten.



Nach dem Erstellen werden **zwei Schlüssel angezeigt**:
- **Access Key** (öffentlicher Schlüssel)
- **Secret Key** (privater Schlüssel)

⚠️ **Wichtig:**  
Der **Secret Key wird nur einmal angezeigt**.  
Notieren oder speichern Sie beide Keys sorgfältig. Diese werden gleich noch **sehr relevant** sein.

## Upload von Dateien in den STACKIT Object Storage (CLI)

Da der Upload von Dateien **nicht über die Weboberfläche** erfolgt, wird in diesem Versuch ein Kommandozeilen-Werkzeug verwendet.


### Voraussetzungen für den Upload

Für den Upload wird das **s3 cmd Tool** benötigt.

- Download: https://s3tools.org/s3cmd
- Installation mit **Standard-Einstellungen** durchführen
- Nach der Installation ein **neues Terminal / Eingabeaufforderung** öffnen

**Im Terminal folgenden Befehl ausführen:**

```bash
s3cmd --configure
```

**Geben Sie die Werte wie folgt ein:**

```bash
Access Key → eigener Access Key
Secret Key → eigener Secret Key
Default region name   → eu01
S3-Endpoint → object.storage.eu01.onstackit.cloud
DNS-style bucket+hostname: %(bucket)s.object.storage.eu01.onstackit.cloud
Encryption password: **Enter drücken**
Path to GPG program [/usr/bin/gpg]: **Enter drücken**
Use HTTPS Protocol: Yes
HTTP Proxy server name: **Enter drücken**
Test access with supplied credentials? [Y/n]: n
Save settings? [y/N]: y
```
## Upload des Videos in den Bucket

 Laden Sie das unter folgender URL verfügbare Testvideo herunter:
 https://www.mt.hs-rm.de/testsignals/mvs-2026S/STEM2-Clip-MVS-STACKIT.mxf


Geben Sie in der Console nun folgenden Befehl ein 

```bash
s3cmd put STEM2-Clip-MVS-STACKIT.mxf s3://IHR_BUCKETNAME/Versuch1/
```
**Auf der Weboberfläche unter dem dem Reiter Bucket wird diese Datei nicht angezeigt!**

!!! question "Frage 1.2"
    Wie können Sie nun herausfinden, ob der Upload wie geplant funktioniert hat? 
    
    Recherchieren Sie den benötigten s3cmd Befehl und nehmen Sie einen Screenshot der Ausgabe in Ihre Ausarbeitung auf

