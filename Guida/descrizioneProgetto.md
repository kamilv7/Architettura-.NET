La struttura base sarà organizzata come segue:

```plaintext
|-- SocialMedia.Api
|    |-- Controllers
|–– SocialMedia.Core
|    |–– DTOs
|    |–– Entities
|    |–– Enumerations
|    |–– Exceptions
|    |–– Interfaces
|–– SocialMedia.Infrastracture
|    |–– Data
|    |–– Filters
|    |–– Repositories
|–– Tests
|    |-- SocialMedia.IntegrationTests
|    |-- SocialMedia.UnitTests
```

# Descrizione dei livelli

## SocialMedia.API

È il Presentation Layer del progetto, che implementa una Web API per gestire l'interazione con il mondo esterno. Qui troviamo i Controllers, responsabili di ricevere richieste, elaborarle e restituire risposte.

## SocialMedia.Core

Questo livello rappresenta il Core Layer (o dominio logico). È il cuore dell'applicazione e contiene:

- **DTOs (Data Transfer Objects)**: oggetti per il trasferimento dei dati tra i livelli.
Entities: rappresentazioni delle entità di dominio.

- **Enumerations**: tipi enumerativi utilizzati nel dominio.

- **Exceptions**: classi per gestire errori specifici del dominio.

- **Interfaces**: contratti che definiscono il comportamento dei componenti del dominio.

Il Core Layer non dipende da altri livelli ed è progettato per essere il più autonomo possibile.

## SocialMedia.Infrastructure

Questo livello rappresenta l’Infrastructure Layer. Contiene implementazioni concrete e dettagli tecnici come:

- **Data**: configurazioni per l'accesso ai dati (es. DbContext per Entity Framework).

- **Filters**: logiche di filtraggio e validazione specifiche.

- **Repositories**: implementazioni dei pattern di accesso ai dati.

## Tests

Include i test dell'applicazione, organizzati in:

- **IntegrationTests**: test di integrazione per verificare il funzionamento congiunto di più componenti.
- **UnitTests**: test unitari per verificare il corretto funzionamento delle singole unità.

# Relazioni tra i Livelli

- **Presentation Layer (SocialMedia.Api)**
Interagisce direttamente con i livelli sottostanti (Core e Infrastructure). È progettato per accedere sia alla logica di dominio (Core) che ai dettagli tecnici (Infrastructure).

- **Core Layer (SocialMedia.Core)**
È il livello più interno e non dipende da alcun altro livello. Fornisce il nucleo logico dell'applicazione.

- **Infrastructure Layer (SocialMedia.Infrastructure)**
Dipende solo dal Core Layer, implementando dettagli tecnici e accesso ai dati necessari per il dominio logico.