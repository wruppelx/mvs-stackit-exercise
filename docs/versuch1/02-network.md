# Netzwerk anlegen
Für die Instanzen, mit denen Sie im Rahmen dieser praktischen Übungen arbeiten, muss zunächst ein Netzwerk angelegt werden.

Achten Sie darauf, dass in der Statusleiste Ihr Projekt `[HDS-Nutzername]-project` und die Region `EU01` ausgewählt sind.

## Subnetz anlegen

Wählen Sie dann im STACKIT Portal im Bereich "NETWORKING" den Eintrag "Network" aus, klicken Sie auf "Create Network" und tragen Sie folgende Parameter ein:

| Feld                           | Eingabe                                         |
|------------|-----------------------|
| Name | [HDS-Nutzername]-network |
| Netzmaske | 24 bit |

![Create Network](../assets/Versuch1/stackit_create_network.jpg)

Klicken Sie anschließend auf "Order fee-based"

!!! question "Aufgabe 1.1"
    Dokumentieren Sie den Ihnen zugewiesenen Adressbereich, die Netzmaske, die konfigurierten DNS-Server sowie die Routing-Tabelle. 
    
