# ReadIt-App 📚

A simple ASP.NET Core Razor Pages web app for browsing a book catalog, built with Entity Framework Core and designed to be deployed against Azure SQL, with Redis planned for shopping-cart support.

## Features

- **Book catalog** — displays a table of books (name, author, pages, price, stock) on the home page
- **Seed data loader** — a "Load Books to DB" button populates the database with a starter set of books via `BookLoader`
- **Add to cart** — per-book "Add to shopping cart" button on in-stock items (cart persistence via Redis is scaffolded but currently commented out)
- **Flexible data layer** — Entity Framework Core configured to run against an in-memory database out of the box, with SQL Server and SQLite packages included for swapping to a real database
- **Azure-ready** — includes step-by-step instructions for migrating from the in-memory store to Azure SQL

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | ASP.NET Core Razor Pages (.NET 8) |
| ORM | Entity Framework Core 6 |
| Database | In-Memory (default) / SQL Server / SQLite |
| Caching / Cart | ServiceStack.Redis |
| Frontend | Razor views (`.cshtml`), Bootstrap-style layout |

## Project Structure

```
ReadIt-App/
├── Data/
│   └── BookContext.cs        # EF Core DbContext
├── Models/
│   └── Book.cs                # Book entity
├── Pages/
│   ├── Index.cshtml           # Book catalog page
│   ├── Index.cshtml.cs        # Catalog page logic (list, add-to-cart, load)
│   ├── Weather.cshtml         # Sample weather page
│   └── Shared/                # Shared layout/partials
├── BookLoader.cs               # Seeds the database with sample books
├── Program.cs                  # App entry point
├── Startup.cs                  # Service configuration & middleware pipeline
├── appsettings.json             # Connection strings & config
├── connect_azure_sql.txt        # Steps to switch to Azure SQL
└── catalog.csproj               # Project file
```

## Getting Started

### Prerequisites

- [.NET 8 SDK](https://dotnet.microsoft.com/download)

### Run locally

```bash
git clone https://github.com/oyeniffy/ReadIt-App.git
cd ReadIt-App
dotnet restore
dotnet run
```

The app runs with an **in-memory database** by default, so no setup is required. Open the app in your browser (see the console output for the URL, typically `https://localhost:5001`), then click **"Load Books to DB"** to seed some sample titles.

### Switching to Azure SQL

The app ships with an in-memory database enabled by a flag in `Startup.cs`:

```csharp
private bool useInMemory = true;
```

To connect to a real Azure SQL database instead:

1. Set `useInMemory` to `false` in `Startup.cs`.
2. Add your connection string under `ConnectionStrings:BooksDB` in `appsettings.json`.
3. Install the EF Core migrations tool and apply migrations:

   ```bash
   dotnet tool install --global dotnet-ef
   dotnet ef migrations add -c BookContext InitialCreate
   dotnet ef database update
   ```

Full steps are also documented in [`connect_azure_sql.txt`](./connect_azure_sql.txt).

### Enabling Redis (shopping cart)

Shopping cart persistence is scaffolded using `ServiceStack.Redis` but currently commented out in `Pages/Index.cshtml.cs`. To enable it:

1. Add your Redis connection string under `Redis:ConnectionString` in `appsettings.json`.
2. Uncomment the Redis-related blocks in `IndexModel` (`OnGet` and `OnPostAddToShoppingCart`).

## Data Model

The `Book` entity includes:

| Field | Type | Description |
|---|---|---|
| `ID` | int | Primary key |
| `Name` | string | Book title |
| `Author` | string | Author name |
| `Pages` | int | Page count |
| `ImageUrl` | string | Cover image filename |
| `Price` | double | Price |
| `InStock` | int | Units available |

## Notes

- This is a learning/demo project, so secrets (connection strings) are left as placeholders in `appsettings.json` — do not commit real credentials.
- `useInMemory = true` resets book data on every app restart; switch to a persistent database for anything beyond local testing.

## License

No license file is currently included — add one (e.g. MIT) if you plan to share or open this project up for contributions.
