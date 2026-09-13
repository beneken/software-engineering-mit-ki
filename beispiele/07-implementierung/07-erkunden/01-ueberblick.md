# Erster Überblick

## Prompt

Lies dir bitte die Dateien des Projekts genau durch und gib mir eine kurze
Zusammenfassung, was die Software macht und welche Technologien eingesetzt werden.

## Kommentar
Analyse ist korrekt.

## Ergebnis (Codex)

### Überblick über das System

#### Zweck und Funktion

Das Projekt ist eine beispielhafte, modular aufgebaute Buchhaltungssoftware zur Verarbeitung von Eingangsrechnungen als PDF. Sie zeigt unter anderem Dependency Injection, verschiedene Entwurfsmuster und eine an einem C4-Modell orientierte Service-Architektur.

Der Verarbeitungsablauf ist grundsätzlich:

1. Der **Scanner Service** überwacht ein Eingangsverzeichnis. Neue PDF-Dateien werden mit Apache PDFBox geöffnet, auf verschlüsselte Dokumente geprüft und in Text, Dateiname, PDF-Version und Metadaten umgewandelt.
2. Die aufbereiteten Dokumentdaten werden über **RabbitMQ** an den Buchhaltungsdienst gesendet.
3. Der **Accounting Service** liest die Dokumentdaten, erzeugt anhand des Rechnungsmodells ein JSON-Schema und lässt den Rechnungstext von einem OpenAI-kompatiblen KI-Dienst in strukturiertes JSON umwandeln.
4. Die erkannte Rechnung wird validiert und in **PostgreSQL** gespeichert. Lieferanten werden dabei wiederverwendet bzw. neu angelegt; Rechnungspositionen werden separat persistiert.
5. Zusätzlich stellt der Accounting Service eine REST-API für Rechnungen und Lieferanten bereit.
6. Der **Archive Service** nimmt verarbeitete Nachrichten entgegen und schreibt sie als JSON-Dateien in ein Ausgabeverzeichnis.

#### Eingesetzte Technologien

- Java 21
- Gradle als Multi-Project-Build
- Spring Boot 3.5 mit Spring Web, Spring AMQP und Spring Data JPA
- RabbitMQ 3 mit Management-UI zur asynchronen Kommunikation zwischen den Services
- PostgreSQL als relationale Datenbank; H2 ist für Laufzeit-/Testzwecke eingebunden
- Apache PDFBox zur PDF-Textextraktion und zum Lesen von PDF-Metadaten
- OpenAI Java SDK über einen konfigurierten, OpenAI-kompatiblen KI-Endpunkt
- Jackson für JSON-Verarbeitung und JSON-Schema-Generierung
- Hibernate/JPA für Persistenz und Bean Validation für Eingabeprüfungen
- Lombok zur Reduzierung von Java-Boilerplate
- SLF4J und Logback für Logging
- JUnit 5, Mockito, Awaitility und JaCoCo für Tests und Testabdeckung
- Docker und Docker Compose für RabbitMQ, PostgreSQL sowie die drei Services
- Springdoc OpenAPI/Swagger für die REST-API-Dokumentation

#### Architekturhinweis

Die gemeinsame Bibliothek `shared` kapselt Konfiguration und RabbitMQ-Zugriff. Die Services sind als eigenständige Java-/Docker-Anwendungen umgesetzt. Im aktuellen Quellstand wirken die Queue-Namen zwischen Scanner, Accounting und Archive teilweise uneinheitlich (`InvoiceInput`/`DocumentInputQueue` sowie `ProcessedDocumentQueue`/`ProcessedInvoices`); für einen durchgängigen Betrieb sollten diese Namen vereinheitlicht oder zentral konfiguriert werden.
