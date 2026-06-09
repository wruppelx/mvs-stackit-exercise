## Transcodierung über eine virtuelle Maschine (VM)

Im folgenden Abschnitt wird die **Transcodierung eines Videos mithilfe einer virtuellen Maschine (VM)** durchgeführt.  
Dabei wird das zuvor in den Object Storage hochgeladene Video auf eine VM geladen, dort verarbeitet und anschließend wieder im Object Storage abgelegt.

Die VM übernimmt in diesem Versuch die Rolle eines **Rechenknotens**, auf dem eine Transcoding-Software ausgeführt wird. Im Gegensatz zu vollständig verwalteten Cloud-Services bietet dieser Ansatz volle Kontrolle über:
- eingesetzte Software
- Transcoding-Parameter
- Ablauf des Workflows

![S3 Dashboard](../assets/versuch1/StackApp.jpg)

### Ablauf im Überblick

Der Transcoding-Prozess besteht aus vier klaren Schritten:

1. **Bereitstellung der VM**  
   Eine virtuelle Maschine mit Linux-Betriebssystem wird gestartet und dient als Arbeitsumgebung.

2. **Übertragung der Quelldatei**  
   Das Originalvideo wird aus dem Object Storage auf die VM heruntergeladen.

3. **Transcodierung**  
   Mithilfe eines Kommandozeilenwerkzeugs (FFmpeg) wird das Video in ein alternatives Ausgabeformat bzw. eine andere Auflösung umgewandelt.

4. **Rücktransfer der Ergebnisse**  
   Die transcodierte Videodatei wird wieder in den Object Storage hochgeladen und steht dort für die weitere Verarbeitung oder Auslieferung bereit.


## Netzwerk

**Für dieses Praktikum ist bereits ein virtuelles Netzwerk vorhanden, das verwendet werden kann.
Ein eigenes Netzwerk muss nicht erstellt werden..**

**Bitte navigieren SIe an der linken Seite zu dem Reiter richtung Network:**

![S3 Dashboard](../assets/versuch1/navinet.jpg)

Das vorhandene Netzwerk muss nicht manuell mit anderen Komponenten verbunden werden.

Bei der späteren Erstellung von Rechenressourcen (z. B. Compute-Instanzen, Transcoder oder Services) wird dieses Netzwerk direkt ausgewählt.
Die jeweilige Ressource wird dadurch automatisch Teil des Netzwerks.

Eine separate Kopplung oder zusätzliche Konfiguration des Netzwerks ist nicht erforderlich.

<div style="
  border: 2px solid #ffffff;
  padding: 14px;
  border-radius: 6px;
  margin: 14px 0;
">
  <span style="color:cyan; font-weight:bold; font-size:1.2em;">
    Aufgabe 3:
  </span><br>
  Überprüfen Sie den Ihnen zugewiesenen Adressbereich, die konfigurierten DNS-Server sowie die Routing-Tabelle. Dokumentieren Sie Ihre Ergebnisse in der Abgabemappe.<br>
</div>


## SSH-Schlüssel für den Serverzugang erstellen

Für den Zugriff auf den Linux-Server wird eine Anmeldung per **SSH-Schlüssel** verwendet. Dabei besteht ein Schlüssel immer aus einem **privaten** und einem **öffentlichen** Teil. In der STACKIT-Weboberfläche wird **nur der öffentliche Schlüssel** hinterlegt.

#### Schritt 1: SSH-Schlüsselpaar erzeugen (plattformunabhängig)

Das SSH-Schlüsselpaar wird über den folgenden Online-Generator erzeugt:

https://8gwifi.org/sshfunctions.jsp

![S3 Dashboard](../assets/versuch1/keykeykey.jpg)

1.Öffnen Sie die oben genannte Webseite.
2.Wählen Sie als Algorithmus RSA.
3.Erzeugen Sie ein neues Schlüsselpaar.
4. Laden Sie:
- den Public Key
- den Private Key

### Schritt 2: Key in STACKIT ablegen

Speichern Sie sowohl den public-Key als auch den privat-Key  den privaten Schlüssel lokal auf Ihrem Rechner, z. B. unter folgendem Namen:

```bash
priv-key-stackit.pk
```

Nun wird derSchlüssel standardmäßig im Ordner  

`C:\Users\<Benutzername>\.ssh\`  

gespeichert.

Bei der Servererstellung kann hierfür später das erstellte Schlüsselpaar verwendet werden.

Um sich erfolgreich mit der VM nun verbinden zu können benötigen Sie folgenden Befehl:

```bash
ssh -i <priv-key-file> ubuntu@<public ip der vm>
```
**Für Linux und MACOS gilt:**

```bash
chmod 400 <priv-key-file> ubuntu@<public ip der vm>
```
Wichtig: Der Private-Key darf hierbei nur für Sie lesbar sein!


### Schritt 3: Einrichtung des Keys auf der STACKIT Weboberfläche

Sie haben nun die Möglichkeit das erzeugte Keypaar auf ihrem STACKIT-Konto zu verknüpfen.

**Navigieren sie hierzu als erstes zu ihren Nutzereinstellungen:**

![S3 Dashboard](../assets/versuch1/Benutzereinstellungen.jpg) 

**Unter Passwort&Sicherheit finden Sie nun den Reiter SSH-Schlüssel. Klicken Sie bitte da drauf:**

![S3 Dashboard](../assets/versuch1/keyname.jpg)

**Geben SIe als Namen Ihren Vornamen+Key ein. Zum Beispiel: *leonkey***

**Für den Schlüssel wird ihr Publickey benötigt**

**Anschließend auf Create klicken**





## Security Groups erzeugen

### Konfiguration der Security Group für den SSH-Zugriff

Damit eine Verbindung zur virtuellen Maschine über SSH hergestellt werden kann, muss der entsprechende Netzwerkzugriff explizit erlaubt werden. In STACKIT erfolgt diese Zugriffskontrolle über sogenannte Security Groups. Security Groups definieren, welcher ein- und ausgehende Netzwerkverkehr für eine Ressource erlaubt ist.

Standardmäßig ist bei neu erstellten Servern kein externer Zugriff über Port 22 freigegeben. Aus diesem Grund muss vor dem ersten Login eine passende Regel ergänzt werden.

### Öffnen der Security Groups im STACKIT Portal

Der Zugriff auf die Security Groups erfolgt über das STACKIT Control Center.

Navigieren Sie in der linken Seitenleiste zu:

Networks → Security Groups

![S3 Dashboard](../assets/versuch1/createsecugroup.jpg)

In der Übersicht wird mindestens eine Security Group mit dem Namen `default` angezeigt. Diese Security Group ist in der Regel bereits dem Server zugewiesen und kann für den SSH-Zugriff verwendet werden.

### Bearbeiten der bestehenden Security Group

Klicken Sie in der Liste der Security Groups auf den Namen `default`, um die Detailansicht zu öffnen. In dieser Ansicht werden alle aktuell definierten Sicherheitsregeln angezeigt.

### Hinzufügen einer SSH-Regel

Um den SSH-Zugriff zu erlauben, muss eine neue eingehende Regel erstellt werden. Klicken Sie hierzu auf die Schaltfläche zum Hinzufügen einer neuen Sicherheitsregel. Hier sehen Sie unten ein Plus mit der Beschriftung "Sicherheitsregel erstellen"

Tragen Sie die folgenden Werte ein:

Name: namenachnameRegel -> maxmustermannRegel  
Protokoll: TCP  
Start-Port: 22  
End-Port: 22  
IP-Bereich: 0.0.0.0/0  
Beschreibung: SSH Zugriff

Speichern Sie die Regel nach dem Eintragen der Werte. Die Änderung wird sofort wirksam.


## Virtual Machine erstellen

**Navigieren Sie zu **Computing** / Server**

![S3 Dashboard](../assets/versuch1/createserver.jpg)

**Klicken Sie auf Create Server**

- **Name:** namenachnamevm1337  
- **Availability Zone:** EU01-2  
- **Betriebssystem:** Ubuntu  
- **Version:** Ubuntu 24.04  
- **STACKIT Server Agent:** aktiviert  
- **Leistungsklasse:** Performance Class 2  
- **Volumengröße:** 60 GB  
- **Boot-Volume beim Löschen löschen:** nicht aktivieren  

---

## Flavors

- **Kategorie:** Allgemeine Zwecke  
- **Auswahl:** g2i.2 2 CPU 8GB RAM  

---

## Management

- **Server Backup Management Service:** deaktiviert  
- **Server Update Management Service:** deaktiviert  

**Network**
**Wählen Sie hier ihr vorher erzeugtes Netzwerk aus**

**Initiale Zugangsdaten**

SSH-Schlüssel welcher vorher angelegt wurde eingeben.

**Bestellung**
Einstellungen prüfen

**Kostenpflichtig bestellen anklicken**

Nun sollten SIe ihren erstellten Server sehen können

![S3 Dashboard](../assets/serverrunning.jpg)

## Zuweisung einer öffentlichen IP-Adresse zur virtuellen Maschine

Damit eine virtuelle Maschine aus dem Internet erreichbar ist, muss ihr eine öffentliche IP-Adresse zugewiesen werden. In STACKIT sind Server standardmäßig nur innerhalb des internen Netzwerks erreichbar. Selbst korrekt konfigurierte Security Groups erlauben ohne eine öffentliche IP keinen externen Zugriff, beispielsweise per SSH.

In diesem Abschnitt wird gezeigt, wie eine öffentliche IPv4-Adresse erstellt und mit einer bestehenden virtuellen Maschine verbunden wird.

### Öffnen der Public-IP-Verwaltung

Die Verwaltung öffentlicher IP-Adressen erfolgt direkt über die Serveransicht im STACKIT Portal.

Navigieren Sie im linken Menü des Servers zu:

Network → Public IP Address

![S3 Dashboard](../assets/connip.jpg)


Diese muss zu guter letzt einfach nur noch mit dem Server verbunden werden:

![S3 Dashboard](../assets/ipconnect.jpg)


## Testen der Verbindung zur virtuellen Maschine per SSH

In der Medieninformatik und in medientechnischen Systemen ist es besonders wichtig, technische Konfigurationen nicht nur theoretisch korrekt einzurichten, sondern diese auch praktisch zu überprüfen. Gerade bei verteilten Systemen in der Cloud können kleine Konfigurationsfehler, etwa in der Netzwerk- oder Sicherheitskonfiguration, dazu führen, dass ein System zwar formal existiert, jedoch nicht wie vorgesehen nutzbar ist. Aus diesem Grund ist es gängige Praxis, jeden wesentlichen Einrichtungsschritt durch gezielte Tests gegenzuprüfen, bevor mit der eigentlichen Verarbeitung von Medieninhalten begonnen wird.

Nachdem der virtuellen Maschine eine öffentliche IP-Adresse zugewiesen wurde, kann nun eine Verbindung über das Secure-Shell-Protokoll (SSH) hergestellt werden. SSH ermöglicht einen sicheren, textbasierten Zugriff auf das Linux-System der virtuellen Maschine und wird im weiteren Verlauf des Versuchs zur Installation und Ausführung der Transcoding-Software verwendet.


### Test der Netzwerkverbindung

Vor dem eigentlichen Login wird geprüft, ob der Server über den SSH-Port erreichbar ist. Dazu wird auf dem lokalen Rechner folgendes Kommando ausgeführt:

```bash
ssh ubuntu@<öffentliche-IP-Adresse>
```
Folgende Ausgabe ist hierbei zu erwarten:

![S3 Dashboard](../assets/sshtest.jpg)



**Alle Schritte verliefen erfolgreich? Dann geht es nun weiter zum Transcoding!**



