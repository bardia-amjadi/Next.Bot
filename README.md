# Next.Bot Documentation

## 1. Project Overview
**Next.Bot** is a Telegram bot framework with advanced features, providing developers with:

- Command and section (Controller) management  
- User authentication and Cookie management  
- Database integration using Entity Framework Core  
- Markdown-based view rendering with model support  
- Bot response management using `BotResponse` class  

Built with **C#** and **.NET 7+**.

---

## 2. Architecture

### 2.1 Main Classes
| Class | Responsibility |
|-------|----------------|
| `BotApp` | Core of the bot, manages Telegram connection, registers controllers, executes commands |
| `Controller` | Defines sections and holds related commands |
| `ActionAttribute` | Marks a method as a Telegram command |
| `CookieManager` | Handles user authentication and stores cookies |
| `BotResponse` | Bot response model, including text, error status, and target chat IDs |
| `AppDbContext` | EF Core DbContext, supports dynamically registered entities |
| `DatabaseOption` | Database connection configuration |
| `ViewRenderer` | Renders Markdown templates with model data, loops, and conditions |

---

### 2.2 Bot Execution Flow
1. **Create BotApp:**  
   ```csharp
   var bot = new BotApp();
   ```
2. Setup and Execution

3. Register Controllers
```csharp
bot.MapController(new MainController());
```

4. Register Entities and Configure Database
```csharp
bot.AddEntity<User>();
bot.UseDatabase(new DatabaseOption { ConnectionString = "...", Provider = "SqlServer" });
```

5. Run Migrations
```csharp
bot.Migrate();
```

6. Start the Bot
```csharp
await bot.Run();
```

## 3. Components

### 3.1 Command Management
- Methods in Controllers marked with `[Action]` are registered as bot commands.  
- `BotApp.ExecuteCommand` parses messages and executes the matched method.  
- If a command requires authentication, it checks the user before execution.  

### 3.2 Cookies and Authentication
- `CookieManager` manages creating, reading, and deleting user cookies.  
- Simple authentication checks for the `"IsAuthenticated"` key in the cookie.  

### 3.3 Database Management
- Use `AddEntity<T>()` to register entities.  
- `UseDatabase` applies database connection settings.  
- `Migrate` applies model changes to the database.  
- Supported providers: SqlServer, PostgreSQL, SQLite.  

### 3.4 View Rendering
- Views are Markdown templates stored in the `Views` folder.  
- `ViewRenderer.Render("viewName", model)` can:  
  - Replace model properties: `@Model.Property`  
  - Process loops: `@foreach(var item in Model.Items) { ... }`  
  - Handle simple conditions: `@if(Model.Age > 18) { ... }`  

### 3.5 Bot Response
- `BotResponse` includes:  
  - `Text`: the response text  
  - `IsError`: whether the response is an error  
  - `ChatIds`: target chat IDs for the response  
- This allows sending messages to multiple users simultaneously.  

---

## 4. Technical Notes
- Controllers must inherit from the `Controller` base class.  
- Duplicate commands are checked at registration and throw an exception.  
- All operations are async and support `CancellationToken`.  
- Markdown special characters are escaped automatically.  

---

## 5. Program File
```csharp
var bot = new BotApp();
bot.MapController(new MainController());
bot.AddEntity<User>();
bot.UseDatabase(new DatabaseOption { 
    ConnectionString = "Server=.;Database=BotDb;Trusted_Connection=True;", 
    Provider = "SqlServer" 
});
bot.Migrate();
await bot.Run();
```
