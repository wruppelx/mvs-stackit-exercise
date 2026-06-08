# Fazit und Lernergebnisse

In diesem Praktikum wurde ein vollständiger, praxisnaher Video-on-Demand-Workflow aufgebaut und schrittweise erweitert.
Ausgehend von der reinen Medienverarbeitung bis hin zur performanten Auslieferung über ein Content Delivery Network konnten zentrale Konzepte moderner Medientechnik praktisch erprobt werden.

Zu Beginn lag der Fokus auf der Transcodierung von Videodateien. Dabei wurde nachvollzogen, warum Medieninhalte nicht in einem einzigen Format ausgeliefert werden, sondern in mehrere, auf Endgeräte und Bandbreiten abgestimmte Varianten aufgeteilt werden. Durch die Analyse der erzeugten Dateien mit Kommandozeilenwerkzeugen wurde deutlich, wie Auflösung, Bitrate und Codec-Einstellungen die Eigenschaften eines Videos beeinflussen.

Anschließend wurde die Auslieferung der Medien über ein CDN realisiert. Hierbei wurde der STACKIT Object Storage als Origin genutzt und über Fastly öffentlich erreichbar gemacht.
Dieser Schritt verdeutlichte die Trennung zwischen Speicherung und Distribution von Medieninhalten – ein grundlegendes Prinzip moderner Mediensysteme.

Ein zentraler Bestandteil des Versuchs war das Caching-Verhalten des CDNs. Durch gezielte Tests mit wiederholten HTTP-Anfragen konnte beobachtet werden, wie Inhalte zunächst vom Origin geladen und anschließend direkt von Edge-Servern ausgeliefert werden. Anhand von HTTP-Headern wurde nachvollziehbar, ob eine Anfrage als Cache-Miss oder Cache-Hit behandelt wurde.
Dies zeigte anschaulich, welchen Einfluss CDNs auf Ladezeiten, Skalierbarkeit und die Entlastung des Ursprungssystems haben.

Darüber hinaus wurde ein einfacher automatisierter Reaktionsmechanismus umgesetzt, der ohne komplexe Cloud-Funktionen auskommt.
Allein durch das Zusammenspiel von Object Storage, CDN und HTTP-Anfragen konnte gezeigt werden, dass Änderungen an Medieninhalten automatisch wirksam werden, sobald sie vom CDN erneut angefordert werden.
Dieses Verhalten bildet die Grundlage vieler produktiver Medienworkflows und ist besonders robust, transparent und leicht wartbar.

Insgesamt hat der Versuch verdeutlicht, dass moderne Video-on-Demand-Systeme aus klar getrennten, lose gekoppelten Komponenten bestehen:
Speicherung, Verarbeitung, Distribution und Analyse greifen ineinander, ohne dass jede Komponente den internen Zustand der anderen kennen muss.

Für die Medientechnikpraxis ist insbesondere die Fähigkeit relevant, solche Systeme nicht nur theoretisch zu verstehen, sondern auch zu konfigurieren, zu testen und zu analysieren.
Der Umgang mit Kommandozeilenwerkzeugen, HTTP-Headern und CDN-Konzepten stellt dabei eine essentielle Kompetenz dar, die in realen Produktionsumgebungen täglich benötigt wird.
---

⬅️ **Vorheriges Kapitel:**  
[Automatisierungsbeispiel](04-Automatisierungsbeispiel.md)
