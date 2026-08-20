# Verteilungsarchitektursicht z.B. aus docker-compose.yml

## Prompt
Erstelle bitte auf der Grundlage der Datei docker-compose.yml eine Verteilungsarchitektur-Sicht (Deployment-View im C4-Modell) mit Containern, Schnittstellen und Kommunikationswegen. Bitte verwende PlantUML.

## Kommentar

Generiertes Diagramm ist erstmal eine gute Basis. 
Sollte noch mal deutlich vereinfacht werden. Leider wurde beim
Generieren nicht das C4-Modell verwendet, sondern eine Variante 
des UML-Verteilungsdiagramms (erstmal auch OK).
 
## Ergebnis (Codex, August 2026)
```plantuml
@startuml deploymentview

title Deployment View – Buchhaltungssoftware

skinparam shadowing false
skinparam componentStyle rectangle
skinparam defaultTextAlignment center

legend right
  Deployment-Sicht auf Grundlage von docker-compose.yml
  Externe Konfiguration und Zugangsdaten werden über .env eingebunden.
endlegend

node "Docker Compose Host" as host {

  node "RabbitMQ Container\nrabbitmq:3-management" as rabbitmq {
    component "RabbitMQ Broker\nAMQP" as broker
    component "Management UI\nHTTP :15672" as rabbitmq_ui
  }

  node "PostgreSQL Container\npostgres:latest" as postgres {
    database "PostgreSQL Database\nTCP :5432" as db
  }

  node "Scanner Container\nscannerservice" as scanner {
    component "Scanner Service\nJava 21" as scanner_app
    interface "RabbitMQ AMQP\nTCP :5672" as scanner_amqp
  }

  node "Accounting Container\naccountingservice" as accounting {
    component "Accounting Service\nSpring Boot / Java 21" as accounting_app
    interface "REST API\nHTTP :8080" as accounting_api
    interface "RabbitMQ AMQP\nTCP :5672" as accounting_amqp
  }

  node "Archive Container\narchiveservice" as archive {
    component "Archive Service\nJava 21" as archive_app
    interface "RabbitMQ AMQP\nTCP :5672" as archive_amqp
  }

  folder "./pdf_input" as pdf_input
  folder "./output" as output
}

actor "Dateisystem / Rechnungseingang" as invoice_source
actor "REST-Client" as rest_client
cloud "OpenAI-kompatibler\nKI-Endpunkt" as ai
cloud "Credentials-/Login-API" as credentials

invoice_source --> pdf_input : legt PDF ab
pdf_input --> scanner_app : read-only Volume\nPDF-Dateien

scanner_app --> scanner_amqp : publiziert Rechnungsdaten
scanner_amqp --> broker : AMQP :5672\nQueue InvoiceInput

broker --> accounting_amqp : AMQP :5672\nQueue DocumentInputQueue
accounting_amqp --> accounting_app : verarbeitet Dokument

accounting_app --> db : JDBC / JPA\nPostgreSQL :5432
accounting_app --> ai : HTTPS\nRechnungstext → JSON
accounting_app --> credentials : HTTPS\nLogin / API-Key

accounting_app --> accounting_api : stellt Endpunkte bereit
rest_client --> accounting_api : HTTP :9000 → :8080\nRechnungen / Lieferanten

accounting_app --> broker : publiziert verarbeitete Rechnung\nQueue ProcessedDocumentQueue
broker --> archive_amqp : AMQP :5672\nQueue ProcessedInvoices
archive_amqp --> archive_app : verarbeitet Nachricht
archive_app --> output : schreibt JSON-Datei\nVolume /data/invoices

note right of rabbitmq
  Ports des Hosts:
  - 5672 AMQP
  - 15672 Management UI
end note

note right of postgres
  Port des Hosts:
  - 5432 PostgreSQL
end note

note bottom of scanner
  Startet erst nach gesundem RabbitMQ.
end note

note bottom of accounting
  Startet erst nach gesundem RabbitMQ
  und PostgreSQL.
end note

note bottom of archive
  Startet erst nach gesundem RabbitMQ.
end note

@enduml
```