# Statusmeldungen im automatisierten Workflow
## Einführung

In einem automatisierten Medienworkflow ist es wichtig, jederzeit nachvollziehen zu können, was gerade passiert.
Gerade bei asynchronen Prozessen wie Transcoding oder Dateiübertragungen laufen viele Schritte im Hintergrund ab, ohne dass ein Nutzer aktiv eingreift.

In diesem Versuch wird bewusst auf komplexe Benachrichtigungssysteme verzichtet.
Stattdessen wird ein einfaches, aber praxisnahes Logging-Konzept verwendet, wie es auch häufig in kleineren Produktionsumgebungen oder internen Tools eingesetzt wird.

Die Statusmeldungen werden dabei lokal auf der STACKIT-VM in einer Logdatei protokolliert.
So lässt sich jederzeit nachvollziehen:

-wann eine neue Datei erkannt wurde

-wann das Transcoding gestartet wurde

-wann alle Distributionsformate erfolgreich erzeugt wurden

Dieses Vorgehen ist bewusst einfach gehalten, vermittelt jedoch ein zentrales Prinzip moderner Medientechnik:

**Automatisierung ist nur dann sinnvoll, wenn ihr Ablauf nachvollziehbar bleibt.**

## Grundbegriffe

### Statusmeldung

Eine Statusmeldung beschreibt den aktuellen Zustand eines Prozesses.
Sie gibt Auskunft darüber, was gerade passiert oder welcher Schritt abgeschlossen wurde.

Typische Statusmeldungen in diesem Versuch sind zum Beispiel:

-Upload erkannt

-Transcoding gestartet

-Transcoding abgeschlossen

-Statusmeldungen sind essenziell, um automatisierte Prozesse nachvollziehbar und kontrollierbar zu machen.

### Logging

Logging bezeichnet das systematische Mitschreiben von Statusmeldungen und Ereignissen in einer Logdatei.

In diesem Versuch wird Logging genutzt, um:

-den Ablauf des Workflows zu dokumentieren

-Fehler schneller zu erkennen

-den zeitlichen Verlauf einzelner Schritte nachzuvollziehen

Logdateien werden in der Praxis sehr häufig eingesetzt, da sie einfach umzusetzen und unabhängig von speziellen Cloud-Diensten sind.

### Asynchrone Verarbeitung

Als asynchron bezeichnet man Prozesse, die im Hintergrund ablaufen, ohne den Nutzer blockierend warten zu lassen.

Das Transcoding in diesem Versuch ist ein typisches Beispiel:

-der Auftrag wird gestartet

-die Verarbeitung läuft unabhängig weiter

-das Ergebnis steht erst später zur Verfügung

Statusmeldungen und Logging sind besonders bei asynchronen Prozessen wichtig, da der Fortschritt nicht sofort sichtbar ist.

