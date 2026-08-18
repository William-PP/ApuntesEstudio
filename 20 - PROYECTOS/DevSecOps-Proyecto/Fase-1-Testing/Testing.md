---
type: tarea
state: en-progreso
created: 2026-08-15
updated: 2026-08-15
tags: [devsecops, containers, database, proyecto, api, testing, fase-1]
---

# Fase 1 — Testing Automatizado

## Objetivo
Crear una suite de tests que verifique la corrección y seguridad de la aplicación de forma automatizada, y validar que los controles de seguridad implementados (Fase 0) realmente protejan al sistema — respondiendo al modelado de amenazas: "¿Hicimos un buen trabajo?"

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

| Herramienta | ¿Qué hace? | ¿Para qué sirve? | ¿Cuándo la usamos? |
|------------|------------|------------------|--------------------|
| **xUnit** | Framework de testing de .NET | Correr y agrupar los tests; con `[Fact]` y `[Theory]` marca qué métodos son pruebas | En TODOS los tests (unit, integration, security) |
| **Moq** | Crea "dobles" falsos de interfaces | Testear una clase AISLADA sin depender de sus dependencias reales (BD, servicios externos) | Solo en Unit Tests: `Mock<IUserRepository>()` que responde lo que yo quiero |
| **FluentAssertions** | Assertions con sintaxis legible | Reemplaza `Assert.Equal(x, y)` por `result.Should().Be(x)` — más fácil de leer y mensajes de error más claros | En todos los tests, para el paso Assert |
| **Testcontainers** | Levanta contenedores Docker reales dentro de los tests | Correr los tests contra un SQL Server REAL e idéntico al de producción, sin usar tu BD local | En Integration Tests y Security Tests |
| **WebApplicationFactory** | Arranca la API completa en memoria dentro del test | Hacer requests HTTP reales a la API (controllers, middleware, JWT, CORS) sin levantarla a mano | En Integration Tests: `_client.GetAsync("/api/team")` |
| **coverlet** | Mide cuánto código está cubierto por tests | Saber si te quedaste sin probar partes críticas; meta >70% | Con `dotnet test` y reportgenerator |

> [!info] ¿Por qué tantas herramientas?
> Cada una cubre un nivel distinto de la pirámide. Moq aísla una clase, WebApplicationFactory prueba la API completa, y Testcontainers garantiza que la BD sea la misma que en producción. No son redundantes — son capas.

## Qué es el testing en seguridad

El testing de software es un proceso organizativo fundamental para verificar la corrección, calidad y rendimiento del código crucial para el negocio. En seguridad, el testing **no es solo buscar errores de lógica**: es validar que los controles implementados realmente protejan al sistema, respondiendo a la pregunta del modelado de amenazas: **"¿Hicimos un buen trabajo?"**

> [!tip] El testing como validación del Threat Model
> Si implementamos una defensa contra XSS (sanitización con DOMPurify), se deben hacer pruebas que intenten **evadir ese control**. Si decidimos usar sentencias preparadas contra SQLi, el testing debe incluir **intentos de inyección** para validar que el control es infranqueable. El testing es la herramienta que confirma que las mitigaciones del diseño funcionan de verdad.

## 1. Shift-Left: pruebas de seguridad en el ciclo de vida

La tendencia moderna es **"desplazar a la izquierda"**: integrar las pruebas desde el inicio del desarrollo para reducir costes y riesgos.

| Práctica | Qué analiza | Cuándo se ejecuta | Ejemplos de herramientas |
|----------|-------------|-------------------|--------------------------|
| **SAST** | El código fuente sin ejecutarlo, buscando debilidades | En cada push (CI) | SonarQube, Semgrep, .NET Analyzers |
| **DAST** | La aplicación en ejecución, simulando ataques externos | Contra la app corriendo | OWASP ZAP |
| **SCA** | Dependencias de terceros, buscando vulnerabilidades conocidas en la cadena de suministro | En cada push (CI) | Dependabot, NuGet audit |
| **Escaneo IaC y Contenedores** | Config (Dockerfiles, Terraform) e imágenes, buscando secretos expuestos y malas configuraciones | Antes de desplegar | Trivy, tfsec/checkov, Gitleaks |

> [!warning] Shift-Left ≠ todo en CI
> SAST/SCA son automáticos y baratos → corren en CI. DAST y los escaneos de contenedores son más costosos → se programan (por ejemplo, en cada release o periódicamente). El objetivo es detectar el problema lo antes posible, no hacer todo en el momento más temprano posible.

## 2. Niveles de testing y su aplicación en seguridad

| Nivel | Qué valida | Uso en seguridad |
|-------|-----------|------------------|
| **Unit** | Funciones aisladas | Verificar que un algoritmo de cifrado o una validación de entrada (Regex) acepte SOLO lo permitido |
| **Integration** | La interacción entre módulos | Asegurar que la app se comunique de forma SEGURA con la BD o el Gestor de Secretos |
| **E2E** | Flujos de usuario reales | Confirmar que procesos críticos (registro, recuperación de contraseña) sigan TODOS los pasos de seguridad requeridos |

## 3. Pruebas de rendimiento (Benchmarking)

No basta con que el software sea seguro; también debe ser **fiable**. Miden el rendimiento según estándares predefinidos.

| Tipo | Qué mide | Relación con seguridad |
|------|----------|------------------------|
| **Carga** | Comportamiento con carga esperada | Estabilidad general |
| **Estrés** | El punto de ruptura del sistema | Mitigar DoS → protege el atributo de **Disponibilidad** del modelo STRIDE |
| **Escalabilidad** | Cómo maneja el crecimiento de usuarios | Evitar degradación cuando crece la base de usuarios |

## 4. El testing como validación del modelo de amenazas

Pruebas de **regresión de autorización** que garantizan que los cambios nuevos no rompan las reglas de acceso existentes. Si una mitigación fue decidida en el diseño (XSS, SQLi, autorización), debe existir un test que intente romperla.

## 5. Estrategia y automatización

La automatización es la clave para que una canalización de **Entrega Continua (CD)** sea exitosa.

- Instrumentar **tests E2E** en flujos principales (login, pagos)
- Instrumentar **tests unitarios** en funciones que manejen información sensible (transacciones monetarias)
- Instrumentar **tests de integración** en cada punto de conexión externa para evitar la propagación de errores

> [!note] En resumen
> El testing profesional combina herramientas automáticas (**HaT** — Hardware/automated Testing) con el análisis experto humano (**TaH** — Testing as Human) para garantizar que el software no solo funcione, sino que sea resistente a ataques y resiliente ante fallos.

## Cómo se conecta con lo que ya hicimos (Fase 0)

Lo que implementamos en Fase 0 se puede y se debe testear:

| Control de Fase 0 | Cómo lo testea cada herramienta |
|-------------------|--------------------------------|
| JWT Auth (login, `[Authorize]`) | Integration: login con password incorrecta → 401; crear team sin token → 401 |
| Input Validation (FluentValidation) | Integration: registro con email vacío → 400 Bad Request |
| Rate Limiting (5/min en auth) | Integration: 6 logins seguidos → 429 |
| UserService (BCrypt, email duplicado) | Unit: password hasheado ≠ texto plano; email duplicado → excepción |
| Health Checks | Integration: GET `/health/ready` → 200 (con BD arriba) |

Estos tests de seguridad son los que en el vault se llaman **Security Tests** (ver sección más abajo): prueban que un usuario sin permiso NO puede hacer algo.

## Unit Tests

> [!tip] La regla de oro del unit testing
> No es "un test por método", es **"un test por comportamiento importante"** (por rama de un `if`).
> Métodos sin lógica propia (pasamano puro al repo) no necesitan tests. Ver caso práctico completo:
> [[20 - PROYECTOS/DevSecOps-Proyecto/Fase-1-Testing/Unit-Tests-UserService]]

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
- [[Seguridad del codigo]]
- [[20 - PROYECTOS/DevSecOps-Proyecto/Fase-1-Testing/Unit-Tests-UserService]]
- [[20 - PROYECTOS/DevSecOps-Proyecto/Referencia/Glosario-DevSecOps]]

## Referencia
- [[20 - PROYECTOS/DevSecOps-Proyecto/Fase-1-Testing/Fase-1-Testing]]
- [[Flujo de un proyecto]]

#devsecops #containers #database #proyecto #api #testing #fase-1
