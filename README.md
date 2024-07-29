# LayerArchitectureDesign-
Developing with Layer Architecture Pattern

Step 1: Create the Solution and Projects
Create a new solution: Open Visual Studio for Mac and create a new solution.
Add projects to the solution:
API Project: This will be the main entry point of the application.
Business Logic Layer (BLL) Project: This will contain the business logic.
Data Access Layer (DAL) Project: This will handle the data access.

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Step 2: Setup Projects
2.1 API Project
Create a new ASP.NET Core Web API project.
Install necessary packages:
sh
Copy code


dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////

2.2 BLL Project
Create a new Class Library project for the Business Logic Layer.
Reference this project from the API project.

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////
2.3 DAL Project
Create a new Class Library project for the Data Access Layer.
Install necessary packages:
sh
Copy code

dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.Design

Reference this project from the BLL project.
/////////////////////////////////////////////////////////////////////////////////////////////////////////////////
Step 3: Implement the Layered Architecture

3.1 Data Access Layer (DAL)
TodoItem.cs:

csharp
Copy code

namespace ToDoApp.DAL
{
    public class TodoItem
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////
AppDbContext.cs:

csharp
Copy code


using Microsoft.EntityFrameworkCore;

namespace ToDoApp.DAL
{
    public class AppDbContext : DbContext
    {
        public DbSet<TodoItem> TodoItems { get; set; }

        public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }
    }
}

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////
3.2 Business Logic Layer (BLL)
ITodoService.cs:

csharp
Copy code
using System.Collections.Generic;
using ToDoApp.DAL;

namespace ToDoApp.BLL
{
    public interface ITodoService
    {
        IEnumerable<TodoItem> GetAll();
        TodoItem GetById(int id);
        void Add(TodoItem item);
        void Update(TodoItem item);
        void Delete(int id);
    }
}

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////

TodoService.cs:

csharp
Copy code
using System.Collections.Generic;
using System.Linq;
using ToDoApp.DAL;

namespace ToDoApp.BLL
{
    public class TodoService : ITodoService
    {
        private readonly AppDbContext _context;

        public TodoService(AppDbContext context)
        {
            _context = context;
        }

        public IEnumerable<TodoItem> GetAll()
        {
            return _context.TodoItems.ToList();
        }

        public TodoItem GetById(int id)
        {
            return _context.TodoItems.Find(id);
        }

        public void Add(TodoItem item)
        {
            _context.TodoItems.Add(item);
            _context.SaveChanges();
        }

        public void Update(TodoItem item)
        {
            _context.TodoItems.Update(item);
            _context.SaveChanges();
        }

        public void Delete(int id)
        {
            var item = _context.TodoItems.Find(id);
            if (item != null)
            {
                _context.TodoItems.Remove(item);
                _context.SaveChanges();
            }
        }
    }
}

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////


3.3 API Layer
Startup.cs:

csharp
Copy code


using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using ToDoApp.DAL;
using ToDoApp.BLL;
using Microsoft.EntityFrameworkCore;

namespace ToDoApp.API
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContext<AppDbContext>(options =>
                options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

            services.AddScoped<ITodoService, TodoService>();

            services.AddControllers();
        }

        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseRouting();
            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllers();
            });
        }
    }
}

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////


TodoController.cs:

csharp
Copy code



using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using ToDoApp.BLL;
using ToDoApp.DAL;

namespace ToDoApp.API.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class TodoController : ControllerBase
    {
        private readonly ITodoService _todoService;

        public TodoController(ITodoService todoService)
        {
            _todoService = todoService;
        }

        [HttpGet]
        public IEnumerable<TodoItem> Get()
        {
            return _todoService.GetAll();
        }

        [HttpGet("{id}")]
        public ActionResult<TodoItem> Get(int id)
        {
            var item = _todoService.GetById(id);
            if (item == null)
            {
                return NotFound();
            }
            return item;
        }

        [HttpPost]
        public ActionResult<TodoItem> Post(TodoItem item)
        {
            _todoService.Add(item);
            return CreatedAtAction(nameof(Get), new { id = item.Id }, item);
        }

        [HttpPut("{id}")]
        public IActionResult Put(int id, TodoItem item)
        {
            if (id != item.Id)
            {
                return BadRequest();
            }

            _todoService.Update(item);
            return NoContent();
        }

        [HttpDelete("{id}")]
        public IActionResult Delete(int id)
        {
            _todoService.Delete(id);
            return NoContent();
        }
    }
}

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////


Step 4: Configure Database
Add the connection string in appsettings.json of the API project:


json
Copy code


{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ToDoDb;Trusted_Connection=True;MultipleActiveResultSets=true"
  }
}

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////


Create and apply migrations:

sh
Copy code

dotnet ef migrations add InitialCreate --project ../ToDoApp.DAL
dotnet ef database update --project ../ToDoApp.DAL

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////


Step 5: Run the Project
Set the API project as the startup project.
Run the application.
You now have a basic To-Do List API with a layered architecture using .NET Core 8.0.
