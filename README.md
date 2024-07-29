# Layer Architecture Design

This project is a simple To-Do List API built with C# and .NET Core 8.0, utilizing a layered architecture. The API allows users to perform CRUD operations on To-Do items.

## Table of Contents

- [Getting Started](#getting-started)
- [Project Structure](#project-structure)
- [Setup and Configuration](#setup-and-configuration)
- [Usage](#usage)
- [API Endpoints](#api-endpoints)
- [Contributing](#contributing)
- [Contributors](#contributors)
- [License](#license)

## Getting Started

To get a local copy up and running, follow these simple steps.

### Prerequisites

- [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)

### Installation

1. Clone the repository:
   ```sh
   git clone https://github.com/your-username/todo-list-api.git


###

## Project Structure
### The project is structured into three main layers:

1. API Layer: This is the main entry point of the application. It handles HTTP requests and responses.
2. Business Logic Layer (BLL): This layer contains the business logic.
3. Data Access Layer (DAL): This layer handles the data access and database interactions.
4. /ToDoApp
│
├── ToDoApp.API
│   ├── Controllers
│   ├── Program.cs
│   └── Startup.cs
│
├── ToDoApp.BLL
│   ├── Interfaces
│   └── Services
│
└── ToDoApp.DAL
    ├── Models
    └── AppDbContext.cs

   
   
## Setup and Configuration
### 1. Configure Database Connection

Add your database connection string in the appsettings.json file of the API project:

{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ToDoDb;Trusted_Connection=True;MultipleActiveResultSets=true"
  }
}

### 2. Apply Migrations

Create and apply the initial database migrations:

dotnet ef migrations add InitialCreate --project ../ToDoApp.DAL
dotnet ef database update --project ../ToDoApp.DAL

## Usage
### 1. Set the API project as the startup project.
### 2. Run the application.
### 3. The API will be available at https://localhost:5001.

## API Endpoints
### GET /api/todo: Get all To-Do items.
### GET /api/todo/{id}: Get a To-Do item by ID.
### POST /api/todo: Create a new To-Do item.
### PUT /api/todo/{id}: Update an existing To-Do item.
### DELETE /api/todo/{id}: Delete a To-Do item by ID.


## Example Requests
### GET /api/todo
curl -X GET https://localhost:5001/api/todo

### POST /api/todo
curl -X POST https://localhost:5001/api/todo \
     -H "Content-Type: application/json" \
     -d '{"name": "New Task", "isComplete": false}'

### PUT /api/todo/{id}
curl -X PUT https://localhost:5001/api/todo/1 \
     -H "Content-Type: application/json" \
     -d '{"id": 1, "name": "Updated Task", "isComplete": true}'
     
### DELETE /api/todo/{id}
curl -X DELETE https://localhost:5001/api/todo/1

## Contributing
Contributions are what make the open-source community such an amazing place to learn, inspire, and create. Any contributions you make are greatly appreciated.

## Fork the Project
### 1. Create your Feature Branch (git checkout -b feature/AmazingFeature)
### 2. Commit your Changes (git commit -m 'Add some AmazingFeature')
### 3. Push to the Branch (git push origin feature/AmazingFeature)
### 4. Open a Pull Request

## 5. Contributors
### Thanks to the following people who have contributed to this project:

#### Kadae Aung (Software Engineer)
#### Tin Htoo (Tester)
#### Hein Zin Myo Aung

## License
### Distributed under the MIT License. See LICENSE for more information.

Replace placeholders like `your-username`, `Contributor Name`, and the license link as appropriate for your project.





