# Indice

1. [Struttura del Progetto](#struttura-del-progetto)
2. [Descrizione dei Livelli](#descrizione-dei-livelli)
   - [SocialMedia.Api](#socialmediaapi)
   - [SocialMedia.Core](#socialmediacore)
   - [SocialMedia.Infrastructure](#socialmediainfrastructure)
   - [Tests](#tests)
3. [Relazioni tra i Livelli](#relazioni-tra-i-livelli)
4. [Pacchetti da Installare](#pacchetti-da-installare)
   - [SocialMedia.Infrastructure](#socialmediainfrastructure-1)
   - [SocialMedia.Api](#socialmediaapi-1)
5. [Configurazione](#configurazione)
   - [Scaffolding](#scaffolding)
     - [Protezione della Stringa di Connessione](#protezione-della-stringa-di-connessione)
       1. [Definire la Stringa di Connessione nel file appsettings.json](#1-definire-la-stringa-di-connessione-nel-file-appsettingsjson)
       2. [Utilizzare la Sintassi Name= nel Comando Scaffold](#2-utilizzare-la-sintassi-name-nel-comando-scaffold)
       3. [Configurare il DbContext in Program.cs](#3-configurare-il-dbcontext-in-programcs)
6. [Utils](#utils)
   - [.gitignore](#gitignore)

---

# Struttura del progetto

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

# Pacchetti da installare

Una volta creata la struttura andranno installati i seguenti pacchetti.

### SocialMedia.Infrastructure

- Microsoft.EntityFrameworkCore.SqlServer
- Microsoft.EntityFrameworkCore.Tools

### SocialMedia.Api

- Microsoft.EntityFrameworkCore.Design

# Configurazione

Istruzioni generali per la configurazione.

## Scaffolding

Lo scaffolding consente di generare automaticamente le classi per il modello di dati e il contesto di database a partire da un database esistente.

Esempio di comando per uno scaffolding di base:
`Scaffold-DbContext "Server=(localdb)\MSSQLLocalDB;Database=SocialMedia;Integrated Security = true" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Data`

Se si usa un db già esistente, il comando potrebbe essre simile a questo:
`Scaffold-DbContext "Server=localhost;Database=SocialMedia;User Id=your_username;Password=your_password" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Data`

### Protezione della stringa di connessione

È buona pratica non includere le stringhe di connessione direttamente nel codice sorgente, in quanto potrebbero contenere informazioni sensibili come nomi utente e password. Per garantire una maggiore sicurezza e flessibilità, è possibile spostare la stringa di connessione in un file di configurazione e utilizzare la sintassi `Name=` per leggerla all'interno dell'applicazione.

#### 1. Definire la stringa di connessione nel file `appsettings.json`

In un'applicazione .NET Core o .NET 5+, è possibile definire la stringa di connessione nel file `appsettings.json`. Ecco un esempio di come aggiungere la stringa di connessione nel file:

```json
{
  "ConnectionStrings": {
    "SocialMedia": "Server=localhost;Database=SocialMedia;Integrated Security=true"
  }
}
```

In questo esempio, la stringa di connessione è stata salvata con il nome `SocialMedia`.

#### 2. Utilizzare la sintassi `Name=` nel comando Scaffold

Quando viene eseguito il comando `Scaffold-DbContext`, è possibile fare riferimento alla stringa di connessione definita nel file di configurazione utilizzando la sintassi `Name=`, la quale consente di leggere la stringa da `appsettings.json` o da altre fonti di configurazione.

```bash
Scaffold-DbContext "Name=SocialMedia" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Data
```

#### 3. Configurare il `DbContext` in `Program.cs`

Per consentire all'applicazione di connettersi al database, è necessario configurare il contesto di database (ad esempio, SocialMediaContext) nel file `Program.cs`. Ecco un esempio di configurazione:

```csharp
builder.Services.AddDbContext<SocialMediaContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("SocialMedia")
    ));
```

> ⚠️ **Warning**: Se il progetto è gestito con **Git**, evitare di committare file di configurazione contenenti stringhe di connessione sensibili ([aggiungere una regola in .gitignore](#gitignore) se necessario).

## Miglioramento SocialMediaContext.cs

Il file `SocialMediaContext.cs` può facilmente diventare molto grande, anche con poche tabelle. Ad esempio, con solo tre tabelle si possono superare rapidamente le 150 righe di codice. Per mantenere il codice leggibile e manutenibile, è buona pratica applicare il principio di separazione delle responsabilità, dividendo la configurazione delle entità in file separati.

### Creazione della cartella Configurations

Si inizia creando una nuova cartella `Configurations` all'interno della struttura del progetto. La configurazione delle entità sarà separata in file distinti per ogni entità.

Esempio di struttura del progetto:

```plaintext
|–– SocialMedia.Infrastracture
|    |–– SocialMediaContext.cs
|    |–– Data
|    |      |-- Configurations
|    |      |      |-- PostConfiguration.cs
|    |      |      |-- CommentConfiguration.cs
|    |      |      |-- UserConfiguration.cs
```

### Configurazione attuale

Attualmente, `SocialMediaContext.cs` contiene la configurazione direttamente nel metodo `OnModelCreating`:

```csharp
 protected override void OnModelCreating(ModelBuilder modelBuilder)
 {
     modelBuilder.Entity<Comment>(entity =>
     {
         entity.ToTable("Comentario");
        // Configurazione dettagliata (40+ righe)...
     });

     modelBuilder.Entity<Post>(entity =>
     {
         entity.ToTable("Publicacion");
        // Configurazione dettagliata (40+ righe)...
     });

     modelBuilder.Entity<User>(entity =>
     {
         entity.ToTable("Usuario");
        // Configurazione dettagliata (40+ righe)...
     });
 }
```

Questa configurazione può essere migliorata separando ciascuna entità in una propria classe di configurazione.

### Creazione delle classi di configurazione

Ogni classe di configurazione `[classe]Configuration.cs` dovrà implementare l'interfaccia `IEntityTypeConfiguration<T>` e definire il metodo `Configure(EntityTypeBuilder<T> builder)`. Questo metodo conterrà la configurazione specifica dell'entità.

Esempio: **PostConfiguration.cs**

```csharp
 public class PostConfiguration : IEntityTypeConfiguration<Post>
 {
     public void Configure(EntityTypeBuilder<Post> builder)
     {
         builder.ToTable("Publicacion");
         // Configurazione dettagliata (40+ righe)...
     }
 }
```

Eseguire lo stesso processo per le altre entità.

### Modifica di `SocialMediaContext.cs`

Aggiornare il metodo `OnModelCreating` in `SocialMediaContext.cs` per applicare le configurazioni utilizzando il metodo `ApplyConfiguration`.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.ApplyConfiguration(new CommentConfiguration());
    modelBuilder.ApplyConfiguration(new PostConfiguration());
    modelBuilder.ApplyConfiguration(new UserConfiguration());
}
```

### Vantaggi di questa approccio

- **Leggibilità**: il file `SocialMediaContext.cs` è più compatto e facile da leggere.
- **Manutenibilità**: le configurazioni delle entità sono isolate in file specifici, rendendo più semplice l'aggiornamento o l'aggiunta di nuove entità.
- **Organizzazione**: la separazione delle configurazioni riflette una chiara struttura a responsabilità singola.

Con questo approccio, ogni configurazione è facilmente individuabile e modificabile, migliorando notevolmente la qualità complessiva del codice.

---

# Utils

## .gitignore

Per evitare di includere file indesiderati o sensibili nel repository Git, è importante configurare un file `.gitignore`. Di seguito, un esempio di `.gitignore` adatto a un progetto .NET con Visual Studio, Visual Studio Code e strumenti correlati:

```plaintext
# Visual Studio
.vs/
*.user
*.suo
*.userosscache
*.sln.docstates

# Build Results
bin/
obj/
Debug/
Release/

# NuGet
*.nupkg
*.snupkg
.nuget/
packages/
project.lock.json
project.fragment.lock.json
artifacts/

# Rider
.idea/

# Visual Studio Code
.vscode/
*.code-workspace

# Logs
*.log

# Web.config and other sensitive files
appsettings*.json

# Other
.DS_Store
Thumbs.db
*.tmp
*.bak
*.swp
*~

# Coverage Reports
*.coverage
*.coveragexml
*.coveragejson
*.trx
*.cobertura.xml
coverage.opencover.xml
coverage.opencover.json
TestResults/
```

La regola `appsettings*.json` consente di escludere file di configurazione che potrebbero contenere informazioni sensibili, come stringhe di connessione o chiavi API. Se necessario, mantenere una copia del file `appsettings.json` di esempio (ad esempio, `appsettings.template.json`) da includere nel repository.
