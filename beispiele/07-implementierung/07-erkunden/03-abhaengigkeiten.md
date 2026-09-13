# Analyse der Abhängigkeiten (Dependencies)
## Prompt
Erstelle bitte auf der Grundlage der Datei build.gradle eine Tabelle mit den verwendeten Bibliotheken und Frameworks, ihren Versionen und einer kurzen Beschreibung ihrer Funktion. Bitte recherchiere auch die Lizenz. Die Tabelle hat die Spalten: Name, Version, Beschreibung, Lizenz. Schreibe das Ergebnis in die Datei dependencies.md.

## Ergebnis (Codex im August 2026)

### Verwendete Bibliotheken und Frameworks

Die Tabelle basiert auf den direkten Einträgen in den `build.gradle`-Dateien aller Unterprojekte. Doppelte Einträge wurden zusammengefasst. Wo keine Version direkt angegeben ist, wird sie über das Spring-Boot- bzw. JUnit-BOM-Dependency-Management festgelegt.

| Name | Version | Beschreibung | Lizenz |
|---|---:|---|---|
| Java | 21 | Programmiersprache und Laufzeit; in allen Services über das Gradle Toolchain-Feature verwendet. | GPLv2 + Classpath Exception |
| Spring Boot | 3.5.0 | Grundgerüst für den Accounting Service, inklusive Auto-Konfiguration und eingebettetem Webserver. | Apache License 2.0 |
| Spring Boot Starter Web | 3.5.0 (über Spring Boot) | REST-Webanwendung auf Basis von Spring MVC und eingebettetem Servlet-Container. | Apache License 2.0 |
| Spring Boot Starter AMQP | 3.5.0 (über Spring Boot) | Spring-Integration für AMQP/RabbitMQ. | Apache License 2.0 |
| Spring AMQP / Spring Rabbit | 3.2.5 | RabbitMQ-Zugriff und Messaging-Unterstützung für Spring. | Apache License 2.0 |
| Spring Boot Starter Data JPA | 3.5.0 (über Spring Boot) | Datenzugriff mit Spring Data JPA und Hibernate. | Apache License 2.0 |
| Springdoc OpenAPI Starter WebMVC UI | 2.8.8 | Erzeugt OpenAPI-Dokumentation und eine Swagger-UI für die REST-API. | Apache License 2.0 |
| RabbitMQ Java AMQP Client | 5.25.0 | Direkter AMQP-Client für die gemeinsame RabbitMQ-Messaging-Komponente. | Apache License 2.0 |
| PostgreSQL JDBC Driver | 42.6.0 | JDBC-Treiber für die Verbindung des Accounting Service zu PostgreSQL. | BSD 2-Clause |
| H2 Database | über Spring Boot Dependency Management | Leichte relationale Datenbank, insbesondere für Tests bzw. lokale Laufzeitumgebungen. | EPL 1.0 oder MPL 2.0 |
| Apache PDFBox | 3.0.5 | Lesen von PDF-Dateien, Extraktion von Text und PDF-Metadaten. | Apache License 2.0 |
| Jackson Databind | 2.15.2 | Serialisierung und Deserialisierung von JSON. | Apache License 2.0 |
| Jackson Datatype JSR-310 | 2.20.0 | Jackson-Unterstützung für Java-Zeittypen wie `LocalDate`. | Apache License 2.0 |
| Jackson JSON Schema Jakarta | 2.20.0 | Erzeugung eines JSON-Schemas aus dem Rechnungsmodell. | Apache License 2.0 |
| OpenAI Java SDK | 1.5.1 | Client für Chat-Completions über einen OpenAI-kompatiblen KI-Endpunkt. | Apache License 2.0 |
| SLF4J API | 2.0.9 | Abstraktion für anwendungsweites Logging. | MIT License |
| Logback Classic | 1.5.18 | Konkrete Logging-Implementierung für SLF4J. | EPL 1.0 oder LGPL 2.1 |
| JetBrains Annotations | 24.0.0 | Annotationen wie `@NotNull` zur statischen Analyse und Dokumentation. | Apache License 2.0 |
| Lombok | 1.18.38 | Generiert Boilerplate-Code wie Getter, Setter und Konstruktoren zur Compile-Zeit. | MIT License |
| JUnit Jupiter | 5.10.0 (JUnit BOM) | Test-Framework für Unit- und Integrationstests. | EPL 2.0 |
| JUnit Platform Launcher | 1.10.0 (JUnit BOM) | Startet und entdeckt JUnit-Tests. | EPL 2.0 |
| Mockito Core | 5.20.0 | Mocking von Abhängigkeiten in Tests. | MIT License |
| Mockito JUnit Jupiter | 5.20.0 | Integration von Mockito in JUnit 5. | MIT License |
| Awaitility | 4.3.0 | Lesbare Assertions für asynchrone Vorgänge in Tests. | Apache License 2.0 |
| Spring Boot Starter Test | 3.5.0 (über Spring Boot) | Zusammenstellung typischer Spring-Testwerkzeuge. | Apache License 2.0 |
| Spring Rabbit Test | 3.2.5 (über Spring Boot) | Testunterstützung für Spring-AMQP-Anwendungen. | Apache License 2.0 |
| JaCoCo | über Gradle-Plugin | Ermittelt die Testabdeckung und erzeugt Coverage-Berichte. | EPL 2.0 |
| Spring Dependency Management Plugin | 1.1.7 | Verwaltet kompatible transitive Versionen im Gradle-Build. | Apache License 2.0 |
| Gradle Java / Application / JaCoCo Plugins | Gradle-Version aus Wrapper | Kompilieren, paketieren, starten und testen der Java-Unterprojekte. | Apache License 2.0 |
| Projektmodul `shared` | 1.0-SNAPSHOT | Interne gemeinsame Bibliothek für Konfiguration und RabbitMQ-Komponenten; keine externe Drittanbieterbibliothek. | Projektintern / nicht angegeben |

### Hinweise zur Lizenzrecherche

- Die Lizenzangaben beziehen sich auf die jeweiligen Projekte bzw. Artefakte, nicht auf die Lizenz des Gesamtprojekts.
- Bei Spring-Boot-Startern, H2 sowie JUnit-Artefakten ohne Versionsangabe im Buildskript ist die Version im Buildskript nicht direkt festgeschrieben. Die Tabelle kennzeichnet deshalb die Herkunft der Version statt eine nicht belegte konkrete Patch-Version zu behaupten.
- Lizenzquellen: [Spring](https://spring.io/projects/spring-framework), [Apache PDFBox](https://pdfbox.apache.org/), [Jackson](https://github.com/FasterXML/jackson), [RabbitMQ Java Client](https://github.com/rabbitmq/rabbitmq-java-client), [JUnit 5](https://github.com/junit-team/junit5), [Mockito](https://github.com/mockito/mockito), [Awaitility](https://github.com/awaitility/awaitility), [PostgreSQL JDBC](https://jdbc.postgresql.org/about/license/), [OpenAI Java](https://github.com/openai/openai-java), [Lombok](https://projectlombok.org/), [H2](https://www.h2database.com/html/license.html), [Logback](https://logback.qos.ch/license.html) und [JaCoCo](https://www.jacoco.org/jacoco/trunk/doc/license.html).
