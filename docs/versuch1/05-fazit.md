# Fazit

In Versuch 1 wurden die grundlegenden Prinzipien eines cloudbasierten Transcodierungs-Workflows erläutert und praktisch umgesetzt.  
Ausgehend von einer hochqualitativen Quelldatei wurden mehrere Distributionsformate mit unterschiedlichen Auflösungen und Bitraten erzeugt, die für verschiedene Endgeräte und Bandbreiten geeignet sind.

Die Transcodierung erfolgte dabei nicht über eine grafische Oberfläche, sondern über eine virtuelle Maschine mithilfe von Kommandozeilenwerkzeugen. Die Quelldatei wurde aus dem Bucket heruntergeladen, lokal mit *FFmpeg* verarbeitet und die erzeugten Distributionsdateien anschließend analysiert.

Im Rahmen des Versuchs wurden drei definierte Transcodierungen mit festen Parametern erstellt. Zusätzlich wurde eine weitere Transcodierung durchgeführt, bei der die Parameter eigenständig gewählt und angepasst werden konnten. Dadurch wurde der Umgang mit Transcodierungsparametern vertieft und das Verständnis für deren Auswirkungen auf Qualität, Dateigröße und Abspielbarkeit gestärkt.

Nach Abschluss dieses Versuchs sollten folgende Arbeitsabläufe klar sein:

- Umgang mit einem Bucket als zentralem Speicherort für Medieninhalte  
- Download und Upload von Dateien über eine Kommandozeilenschnittstelle  
- Transcodierung von Videodateien mit *FFmpeg* unter Verwendung unterschiedlicher Parameter  
- Analyse von Audio- und Videoparametern mit *MediaInfo*  

Die in diesem Versuch erlernten Inhalte sind für die Medientechnik von hoher Relevanz. Transcodierungsprozesse, unterschiedliche Distributionsformate sowie der Einsatz von Kommandozeilenwerkzeugen gehören zum Alltag in der Medienproduktion, -verarbeitung und -verteilung. Gerade in automatisierten Workflows und Cloud-Umgebungen ist der sichere Umgang mit CLI-Tools unerlässlich, da viele Prozesse nicht über grafische Oberflächen, sondern skriptbasiert gesteuert werden.

---

## Abgabe

In der Abgabe sollten die im Laufe des Versuchs erzeugten transcodierten Dateien enthalten sein. Dazu gehören die drei standardisierten Distributionsformate sowie die zusätzlich erstellte Datei aus der eigenständigen Transcodierungsaufgabe.


```
📁 
    📁 Versuch1
        📄 testvideo_1080p.mp4
        📄 testvideo_720p.mp4
        📄 testvideo_480p.mp4
    📁 Experimente
        📄 testvideo_eigenes_experiment.mp4
```


Eigene Experimente mit abweichenden Transcodierungsparametern sind ausdrücklich erwünscht und können zusätzlich dokumentiert und abgegeben werden. Diese Zusatzarbeit fließt  positiv in Ihre Bewertung ein.




