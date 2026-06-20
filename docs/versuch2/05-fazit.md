## Lernergebnisse

In diesem Versuch wurde praxisnah nachvollzogen, wie die Auslieferung von Videoinhalten über ein Content Delivery Network (CDN) funktioniert und welchen Einfluss ein CDN auf Performance und Skalierbarkeit hat.

Zunächst wurde ein vollständiger Video-on-Demand-Workflow aufgebaut, bei dem transcodierte Videodateien im **STACKIT Bucket** als Origin abgelegt und anschließend über das **Fastly CDN** öffentlich ausgeliefert wurden. Dabei wurde deutlich, dass Origin und CDN klar getrennte Rollen einnehmen: Der Bucket dient als zuverlässiger, zentraler Speicher, während das CDN für die performante Verteilung an Endnutzer verantwortlich ist.

Ein zentrales Lernergebnis ist das Verständnis des **Caching-Prinzips**. Durch wiederholte Abrufe derselben Datei konnte beobachtet werden, dass Fastly Inhalte nach dem ersten Zugriff auf Edge-Servern zwischenspeichert. Dies ließ sich eindeutig anhand der HTTP-Header (`X-Cache: MISS` → `HIT`, steigender `Age`-Wert) nachvollziehen. Dadurch wurde klar, dass CDNs nicht nur Anfragen weiterleiten, sondern aktiv zur Reduzierung von Latenzen und Origin-Last beitragen.

Mit dem Experiment zur **Time-to-First-Byte (TTFB)** wurde der Performancegewinn messbar gemacht. Der Vergleich zwischen direktem Zugriff auf das Bucket und dem Abruf über das CDN zeigte, dass das CDN in der Regel eine deutlich kürzere TTFB aufweist. Gleichzeitig wurde gelernt, warum mehrere Messungen notwendig sind und weshalb der Median besser geeignet ist als Einzelwerte, um realistische Aussagen über die Performance zu treffen.

Zusätzlich wurde der Umgang mit **HTTP-Headern und DNS-Auflösung** vertieft. Durch die Analyse von `curl`-Ausgaben und DNS-Abfragen wurde sichtbar, welcher Edge-Server die Inhalte ausliefert und wie CDNs je nach Standort unterschiedliche Server auswählen.

Insgesamt vermittelt der Versuch ein praxisnahes Verständnis dafür, warum CDNs ein unverzichtbarer Bestandteil moderner Medienverteilung sind. Die erlernten Konzepte – Bucket als Origin, CDN-Caching, Edge-Auslieferung, Performance-Messung und DNS-Mechanismen – treten in der Medientechnik und in realen Streaming- und Video-on-Demand-Systemen regelmäßig auf und bilden eine wichtige Grundlage für weiterführende Experimente und Projekte.

