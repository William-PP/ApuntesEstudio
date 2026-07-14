# Fase 1 — Testing Automatizado

## Objetivo
Crear una suite de tests que verifique la corrección y seguridad de la aplicación de forma automatizada.

## Tipos de Testing en DevSecOps

```
         ┌─────────────────────────────────────────┐
         │           Testing PYRAMID               │
         │                                         │
         │              /\                         │
         │             /  \        E2E Tests       │
         │            /    \       (pocos)         │
         │           /──────\                      │
         │          /        \    Integration      │
         │         /          \   Tests (algunos)  │
         │        /────────────\                   │
         │       /              \  Unit Tests      │
         │      /                \ (muchos)        │
         │     /──────────────────\                │
         └─────────────────────────────────────────┘
```

## Herramientas

| Herramienta | Propósito |
|------------|-----------|
| **xUnit** | Framework de testing |
| **Moq** | Mockear dependencias |
| **FluentAssertions** | Assertions legibles |
| **Testcontainers** | Containers reales para tests (SQL Server) |
| **coverlet** | Code coverage |

## Unit Tests

### Estructura
```
TuProyecto.Tests/
├── UnitTests/
│   ├── Services/
│   │   ├── TeamServiceTests.cs
│   │   └── PlayerServiceTests.cs
│   └── Helpers/
│       └── ValidationTests.cs
└── IntegrationTests/
    ├── Controllers/
    │   └── TeamControllerTests.cs
    └── Fixtures/
        └── TestDatabaseFixture.cs
```

### Ejemplo de Unit Test
```csharp
public class TeamServiceTests
{
    private readonly Mock<ITeamRepository> _repoMock;
    private readonly TeamService _service;

    public TeamServiceTests()
    {
        _repoMock = new Mock<ITeamRepository>();
        _service = new TeamService(_repoMock.Object);
    }

    [Fact]
    public async Task GetAll_ReturnsListOfTeams()
    {
        // Arrange
        var teams = new List<Team> { new Team { Id = 1, Name = "Millonarios" } };
        _repoMock.Setup(x => x.GetAllAsync()).ReturnsAsync(teams);

        // Act
        var result = await _service.GetAllAsync();

        // Assert
        result.Should().HaveCount(1);
        result.First().Name.Should().Be("Millonarios");
    }
}
```

## Integration Tests

### WebApplicationFactory
```csharp
public class TeamControllerTests : IClassFixture<WebApplicationFactory<Program>>
{
    private readonly HttpClient _client;

    public TeamControllerTests(WebApplicationFactory<Program> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetTeams_ReturnsOk()
    {
        var response = await _client.GetAsync("/api/team");
        response.StatusCode.Should().Be(HttpStatusCode.OK);
    }
}
```

## Testcontainers (SQL Server real)

```csharp
public class TestDatabaseFixture : IAsyncLifetime
{
    private readonly Testcontainer _container;

    public TestDatabaseFixture()
    {
        _container = new TestcontainerBuilder()
            .WithImage("mcr.microsoft.com/mssql/server:2022-latest")
            .WithEnvironment("ACCEPT_EULA", "Y")
            .WithEnvironment("SA_PASSWORD", "YourStrong!Password123")
            .WithPortBinding(1433, true)
            .Build();
    }

    public async Task InitializeAsync() => await _container.StartAsync();
    public async Task DisposeAsync() => await _container.DisposeAsync();
}
```

## Code Coverage

```bash
# Ejecutar con coverage
dotnet test /p:CollectCoverage=true /p:CoverletOutputFormat=cobertura

# Ver reporte
# Instalar reportgenerator global tool
dotnet tool install -g dotnet-reportgenerator-globaltool
reportgenerator -reports:coverage.cobertura.xml -targetdir:coverage
```

### Meta de Coverage
| Tipo | Meta mínima |
|------|------------|
| Line coverage | > 70% |
| Branch coverage | > 60% |
| Method coverage | > 80% |

## Security Tests (tests específicos de seguridad)

```csharp
[Fact]
public async Task Login_InvalidPassword_ReturnsUnauthorized()
{
    var response = await _client.PostAsJsonAsync("/api/auth/login", new
    {
        Email = "test@test.com",
        Password = "wrongpassword"
    });
    response.StatusCode.Should().Be(HttpStatusCode.Unauthorized);
}

[Fact]
public async Task CreateTeam_WithoutAuth_Returns401()
{
    // No enviar token
    var response = await _client.PostAsJsonAsync("/api/team", new { Name = "Test" });
    response.StatusCode.Should().Be(HttpStatusCode.Unauthorized);
}
```

## Comandos útiles
```bash
# Ejecutar todos los tests
dotnet test

# Ejecutar tests de un proyecto específico
dotnet test TuProyecto.Tests/

# Ejecutar un test específico
dotnet test --filter "FullyQualifiedName~TeamServiceTests"

# Ejecutar con verbosity
dotnet test --verbosity normal

# Ejecutar y abrir coverage
dotnet test /p:CollectCoverage=true
```

## Notas personales
- 

## Links Relacionados
- [[00-Resumen-Fase-0]]
- [[DevSecOps-Proyecto/Referencia/Glosario-DevSecOps]]
