# Abhängigkeiten von der OpenAI-API

Die Abhängigkeit vom LLM-Anbieter OpenAI ist im Projekt auf den **Accounting Service** konzentriert. Es gibt sowohl direkte technische Abhängigkeiten vom OpenAI-Java-SDK als auch indirekte fachliche Abhängigkeiten vom erwarteten Prompt- und Antwortformat.

## 1. OpenAI-Java-SDK

In [`src/services/accounting/build.gradle`](C:\Projekte\softKI\codearchitecture\example\src\services\accounting\build.gradle:36) wird das OpenAI-Java-SDK eingebunden:

```gradle
implementation("com.openai:openai-java:1.5.1")
```

Damit ist der Accounting Service direkt von den Klassen und Schnittstellen dieses SDKs abhängig.

## 2. Direkte Integration in `AiProxy`

Die zentrale OpenAI-Integration befindet sich in [`AiProxy.java`](C:\Projekte\softKI\codearchitecture\example\src\services\accounting\src\main\java\com\example\accounting\service\AiProxy.java).

Direkt verwendete SDK-Klassen sind:

- `com.openai.client.OpenAIClient`
- `com.openai.client.okhttp.OpenAIOkHttpClient`
- `com.openai.models.chat.completions.ChatCompletionCreateParams`

### Client-Konfiguration

In `getClient()` wird ein OpenAI-kompatibler Client erzeugt:

```java
String apiKey = credentialsProxy.getApiKey("<TODO>", "<TODO>");

this.openAIClient = OpenAIOkHttpClient.builder()
        .apiKey(apiKey)
        .baseUrl(BASE_URL)
        .build();
```

Die Klasse hängt dabei von zwei Anbieterparametern ab:

```java
private static final String BASE_URL = "<AI Base URL>";
private static final String MODEL = "<AI model name>";
```

Beide Werte sind im aktuellen Quellstand noch Platzhalter.

### LLM-Aufruf

Der eigentliche API-Aufruf erfolgt in [`AiProxy.executeRequest()`](C:\Projekte\softKI\codearchitecture\example\src\services\accounting\src\main\java\com\example\accounting\service\AiProxy.java:32):

```java
ChatCompletionCreateParams params = ChatCompletionCreateParams.builder()
        .model(MODEL)
        .addUserMessage(prompt)
        .build();

getClient().chat().completions().create(params);
```

Die Anwendung ist somit konkret an das Chat-Completions-Modell und die entsprechende OpenAI-SDK-Struktur gekoppelt.

## 3. Fachliche Nutzung in `DocumentProcessor`

[`DocumentProcessor.java`](C:\Projekte\softKI\codearchitecture\example\src\services\accounting\src\main\java\com\example\accounting\DocumentProcessor.java) verwendet `AiProxy` als LLM-Abstraktion.

Die Klasse:

1. erzeugt mit `JsonSchemaGenerator` ein JSON-Schema aus `Invoice.class`,
2. fügt das Schema und den extrahierten Rechnungstext zu einem Prompt zusammen,
3. ruft `aiProxy.executeRequest(prompt)` auf,
4. erwartet gültiges JSON als Antwort und
5. deserialisiert die Antwort direkt in ein `Invoice`-Objekt.

Der zentrale Ablauf befindet sich in [`DocumentProcessor.java:74-79`](C:\Projekte\softKI\codearchitecture\example\src\services\accounting\src\main\java\com\example\accounting\DocumentProcessor.java:74):

```java
if (basePrompt == null) loadBasePrompt();
String prompt = basePrompt + content;

String aiResponse = aiProxy.executeRequest(prompt);
Invoice invoiceObject = objectMapper.readValue(aiResponse, Invoice.class);
```

Hier besteht eine indirekte, fachliche Abhängigkeit von OpenAI bzw. vom verwendeten LLM:

- Das Modell muss den Prompt verstehen.
- Die Antwort muss gültiges JSON sein.
- Die JSON-Struktur muss zum `Invoice`-Modell passen.
- Datumswerte müssen im Format `YYYY-MM-DD` zurückgegeben werden.
- Zusätzlicher Text außerhalb des JSON würde die Deserialisierung stören.

## 4. API-Key-Beschaffung über `CredentialsProxy`

[`CredentialsProxy.java`](C:\Projekte\softKI\codearchitecture\example\src\services\accounting\src\main\java\com\example\accounting\service\CredentialsProxy.java) verwendet nicht direkt die OpenAI-API. Die Klasse ist jedoch indirekt für die OpenAI-Kommunikation erforderlich, weil sie den API-Key beschafft.

Der Ablauf ist:

1. Anmeldung über `<Base URL of API>/api/v1/Login`.
2. Beschaffung eines API-Keys über `<Base URL of API>/api/v1/ApiKey`.
3. Übergabe des API-Keys an `OpenAIOkHttpClient.builder().apiKey(...)`.

Die externe Credentials-API ist ebenfalls nicht vollständig konfiguriert:

```java
private static final String API_BASE_URL = "<Base URL of API>";
```

Zusätzlich enthalten Benutzername und Passwort aktuell `<TODO>`-Platzhalter in `AiProxy.getClient()`.

## Zusammenfassung

| Stelle | Art der Abhängigkeit |
|---|---|
| `accounting/build.gradle` | Direkte Abhängigkeit vom OpenAI-Java-SDK `1.5.1` |
| `AiProxy` | Direkter Aufbau des OpenAI-Clients und direkter Chat-Completions-Aufruf |
| `DocumentProcessor` | Fachliche Abhängigkeit vom Prompt- und JSON-Antwortformat des LLMs |
| `CredentialsProxy` | Indirekte externe Abhängigkeit zur Beschaffung des API-Keys |
| `Invoice` und Jackson-Verarbeitung | Erwartete Zielstruktur der LLM-Antwort |

Die stärkste technische Kopplung liegt in `AiProxy`. Die stärkste fachliche Kopplung liegt in `DocumentProcessor`, da die LLM-Antwort ohne zusätzliche Validierungs- oder Reparaturlogik direkt in das Domänenobjekt `Invoice` deserialisiert wird.

## Auffällige Konfigurationspunkte

- `BASE_URL` ist nicht konfigurierbar, sondern als Konstante hinterlegt.
- `MODEL` ist ein Platzhalter.
- Die Credentials-API-Adresse ist ein Platzhalter.
- Benutzername und Passwort werden aktuell als `<TODO>` übergeben.
- Es ist kein explizites strukturiertes Ausgabeformat bzw. kein OpenAI-Response-Format im Request konfiguriert; die JSON-Ausgabe wird ausschließlich über den Prompt verlangt.
