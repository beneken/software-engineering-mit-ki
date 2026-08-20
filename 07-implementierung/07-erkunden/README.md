# Erkunden von Quelltexten

Da es den Sprachassistenten für Senioren noch nicht gibt, verwende ich
hier zur Analyse Quelltexte aus einer anderen Lehrveranstaltung. Das
System besteht aus mehreren Containern (Docker) bzw. Teilprojekten (Gradle) 
mit einer zentralen Bibliothek. Anhand des Quelltextes proberen wir den Einstieg in eine für uns "neue" Codebase. Siehe mein Repository zur codearchitecture auf github. 

ACHTUNG: Beim Analysieren genau aufpassen, was die KI zu sehen bekommt. 
Häufig finden sich in Entwickler(innen)-Workspaces Dateien wie ".env" mit 
Tokens, Access-Keys und ähnliche Credentials. Diese vorsorglich entfernen!

 - [Überblick gewinnen, was tut die Software?](01-ueberblick.md)
 - [Verteilungsarchitektur, woraus besteht das System?](02-verteilungssicht.md)
 - [Dependencies, von welchen fremden Bibliotheken hängen wir ab?](03-abhaengigkeiten.md)
 - [Komponenten: Aus welchen Komponenten, Schichten bestehen die Container?](04-komponenten.md)