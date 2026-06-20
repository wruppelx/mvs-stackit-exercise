# Versuch 1  
## Grundlagen cloudbasierter Transcoding-Workflows

Dieser Versuch behandelt die grundlegenden Konzepte, den Aufbau sowie die praktische Umsetzung eines cloudbasierten Video-Transcoding-Workflows unter Nutzung von Object Storage und Transcoding-Tools.

## Ziel des Versuchs

Ziel ist es, ein grundlegendes Verständnis für:

- die Architektur cloudbasierter Transcoding-Workflows  
- die Nutzung von Object Storage  
- die Verarbeitung von Videodaten mit FFmpeg  
- sowie die Bewertung der technischen Umsetzung  

zu entwickeln.


In diesem Versuch werden zunächst die grundlegenden Begriffe und Konzepte eines cloudbasierten Transcodierungs-Workflows erläutert. Anschließend wird anhand einer Schritt-für-Schritt-Anleitung ein erster Transcodierauftrag erstellt und ausgeführt. Ziel ist es, nach Abschluss von Versuch 1 ein grundlegendes Verständnis für Cloud-Transcoder zu entwickeln und einen einfachen Video-on-Demand-Workflow in einer europäischen Cloud-Umgebung selbstständig umsetzen zu können.

Der Versuch basiert auf der Cloud-Plattform STACKIT als Infrastruktur-Anbieter sowie dem Content Delivery Network Fastly zur Auslieferung der Medieninhalte.

## Grundbegriffe


### HDS-Nutzername

Ihr HDS-Nutzername dient der eindeutigen Identifikation von Ressourcen und wird unter anderem bei der Benennung von Buckets, Services und weiteren Komponenten eingesetzt.

Die Syntax des HDS-Nutzernamens entspricht derjenigen, die auch bei der Anmeldung im Stud.IP-System verwendet wird. Dadurch ist eine konsistente und nachvollziehbare Zuordnung der angelegten Ressourcen zu einzelnen Studierenden gewährleistet.

Der HDS-Nutzername wird im Verlauf der Versuche mehrfach verwendet und ist daher als fester Bestandteil der jeweiligen Konfigurationen zu verstehen. 

Wennin der Anleitung `[HDS-Nutzername]` als Bezeichner verwendet wird, ersetzen Sie diesen bitte durch Ihren HDS-Nutzernamen.

![HDS](../assets/Versuch1/hdsname.jpg)

### Cloud-Speicher (*Object-Storage*)

![STACKIT Object Storage](../assets/Versuch1/object-storage.png)


Cloud-Speicher funktioniert im Grundprinzip ähnlich wie bekannte Dienste wie Dropbox oder Google Drive: Dateien werden zentral gespeichert und können bei Bedarf wieder abgerufen werden. In professionellen Cloud-Umgebungen kommt dafür jedoch meist Objektspeicher (Object Storage) zum Einsatz. Objectspeicher weren auch als Buckets bezeichnet.

Im Gegensatz zu klassischen Ordnerstrukturen werden die Daten hier als einzelne Objekte gespeichert. 

Für diesen Praktikumsversuch wird STACKIT Object Storage verwendet. Über die Weboberfläche des STACKIT Control Centers oder über standardisierte Schnittstellen (S3-kompatible API) können Dateien hochgeladen und verwaltet werden. In diesem Versuch dient der Objektspeicher als Ablageort für die hochgeladenen Videodateien sowie für die später erzeugten, transcodierten Versionen.

⚠️ Achtung 
Für gespeicherte Daten fallen laufende Kosten an, außerdem können Kosten für den Datenabruf entstehen. Bei den im Rahmen dieses Versuchs verwendeten kleinen Datenmengen sind diese Kosten sehr gering. Bei größeren Projekten mit vielen oder sehr großen Dateien können die Speicher- und Übertragungskosten jedoch deutlich steigen.

### Transcodierer (*über eine Virtual Machine*)

STACKIT stellt keinen eigenen, spezialisierten Transcoding-Dienst bereit. Stattdessen erfolgt die Verarbeitung von Medieninhalten über virtuelle Maschinen, die mit dem Service STACKIT Compute Engine bereitgestellt werden. Eine virtuelle Maschine kann dabei wie ein normaler Server betrachtet werden, auf dem eigene Software ausgeführt wird.

In diesem Praktikumsversuch wird auf einer solchen virtuellen Maschine eine Transcoding-Software (z. B. FFmpeg) installiert. Die VM greift zunächst auf die im STACKIT Bucketten Videodateien zu und lädt diese zur Verarbeitung herunter. Anschließend werden die Videodateien in verschiedene Zielformate und Auflösungen umgewandelt, um eine spätere Wiedergabe auf unterschiedlichen Endgeräten zu ermöglichen.

Nach Abschluss der Transcodierung werden die erzeugten Ausgabedateien wieder im Bucket gespeichert. Von dort aus können sie in einem weiteren Schritt über ein Content Delivery Network (CDN) an die Endnutzer ausgeliefert werden. Die virtuelle Maschine übernimmt somit ausschließlich die Aufgabe der Medienverarbeitung und ist nicht direkt an der Auslieferung beteiligt.

Dieses Vorgehen entspricht einem typischen cloudbasierten Video-on-Demand-Workflow und verdeutlicht, wie Rechenressourcen, Speicher und Auslieferung in der Cloud getrennt voneinander eingesetzt werden.

![FFMPEG Encoding](../assets/Versuch1/ffmpeg-coding.png)

### Log-in

Für diesen Praktikumsversuch erhalten die Studierenden die benötigten Zugangsdaten (Credentials) direkt vom Dozenten. Eine eigene Registrierung ist nicht erforderlich.

Zur Anmeldung auf StackIT gelangt man über diesen Link: https://portal.stackit.cloud/


![Login User](../assets/Versuch1/login-user.jpg)

**Hier kann der bereitgestellte Username aus der E-Mail entnommen werden**

![Login password](../assets/Versuch1/login-pw.jpg)

**Hier kann das bereitgestellte Password aus der E-Mail entnommen werden**

![Login mfa](../assets/Versuch1/mfa-login.jpg)

Hier besteht die Möglichkeit eine Zwei-Faktor-Authentifizierung zu konfigurieren. Diese kann wahlweise über einen E-mail-Code oder eine Authenticator-App erfolgen.

![Landingpage region](../assets/Versuch1/Landingpage-region.jpg)

**Auf der Landingpage sollte in der oberen Leiste das richtige Projekt und die region EU01 - (Deutschland Süd) ausgewählt werden. Für Sie bereits ein Projekt vorhanden sein, welches Ihren HDS-Nutzernamen beinhaltet**


