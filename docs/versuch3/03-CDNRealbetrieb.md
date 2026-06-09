# CDN im Realbetrieb – „Was würde hier wirklich passieren?

## Szenario-Einstieg

Stellen Sie sich vor, das im Versuch aufgebaute System wird nicht nur
für einen Testclip genutzt, sondern für eine echte Plattform:

- Ein neues Video wird veröffentlicht
- Viele Nutzer greifen gleichzeitig darauf zu
- Das Video soll nur für bestimmte Personen sichtbar sein
- Einzelne Nutzer versuchen, die Datei direkt aus dem Object Storage aufzurufen

Ein CDN wie Fastly übernimmt in solchen Situationen weit mehr Aufgaben
als nur das schnelle Ausliefern der Datei

Eine technische Umsetzung ist **nicht erforderlich**.

# Kontext für die Recherche

!!! question "Frage 3.2"
  Sie betreiben das im Versuch aufgebaute Video-on-Demand-System produktiv.
  Recherchieren Sie, wie ein CDN wie <b>Fastly</b> in den folgenden Situationen
  unterstützen kann.

  <br><br>
  Bearbeiten Sie <b>zwei</b> der folgenden Szenarien:
  <ol>
    <li>Ein Video soll nur für einen begrenzten Zeitraum abrufbar sein.</li>
    <li>Ein Video darf nur von bestimmten Nutzergruppen abgerufen werden.</li>
    <li>Plötzlich greifen sehr viele Nutzer gleichzeitig auf ein neues Video zu.</li>
    <li>Ein Video muss kurzfristig gesperrt oder ersetzt werden.</li>
  </ol>

  <br>
  Für jedes gewählte Szenario:
  <ul>
    <li>Beschreiben Sie in eigenen Worten, <b>welches Problem entsteht</b>.</li>
    <li>Recherchieren Sie, <b>welche CDN-Funktion</b> dabei helfen kann.</li>
    <li>Erklären Sie kurz, <b>warum diese Funktion sinnvoll ist</b>.</li>
  </ul>

  <br>

  **Geben Sie die verwendeten Quellen an.**


