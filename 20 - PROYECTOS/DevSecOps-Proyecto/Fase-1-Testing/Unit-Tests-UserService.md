---
type: tarea
state: en-progreso
created: 2026-08-15
updated: 2026-08-15
tags: [testing, devsecops, unit-tests, mocks, bcrypt, guard-clauses, fase-1]
---

# Unit Tests — UserService (caso práctico)

## La regla de oro: un test por comportamiento, no por método

La idea NO es "un test por método" sino **"un test por comportamiento importante"**.
Un método puede tener 3 tests, otro puede tener 0. La regla que simplifica todo:

> **Un test por cada rama de un `if`** (los guard clauses). Si el método no tiene `if`,
> es solo un pasamanos al repositorio — testearlo verifica casi nada.

## Mapeo de métodos → tests (UserService, 97 líneas)

| Método | ¿Tiene lógica propia? | Tests necesarios |
|--------|----------------------|------------------|
| `GetAllAsync` | No — solo pasa datos al repo | 0 (o 1 trivial, bajo valor) |
| `GetByIdAsync` | No | 0 |
| `CreateAsync` | **Sí** (if email duplicado + hash) | 2: duplicado lanza, password se hashea |
| `UpdateAsync` | **Sí** (if usuario no existe) | 2: existe→actualiza, no existe→lanza |
| `DeleteAsync` | **Sí** (if usuario no existe) | 2: existe→borra, no existe→lanza |
| `GetByEmailAsync` | No | 0 |
| `ValidatePasswordAsync` | **Sí** (if email no existe + verify BCrypt) | 3: email no existe, password mala, password buena |

**Total: 9 tests** para el UserService completo. No "1 por método" (serían 7),
sino "1 por rama importante" (9).

> [!note] Pasamano puro
> Los métodos sin `if` (GetAll, GetById, GetByEmail) se quedan sin tests a propósito.
> Son pasamano puro al repositorio — un bug ahí vendría del repo, que es tema de
> integration tests.

## Qué se prioriza testear (en orden)

| Prioridad | Qué se testea | Por qué |
|-----------|---------------|---------|
| **1. Lógica crítica de seguridad** | Auth, User, passwords, roles | Si falla, la app entera está comprometida |
| **2. Lógica de negocio compleja** | Matches, Standings (tabla de posiciones) | Fácil de romper con reglas anidadas |
| **3. CRUD simple** | Crear/leer/actualizar/borrar un Team | Son llamadas directas a la BD, poco margen de error |

La meta de coverage (>70%) no significa "testear todo al 100%". Significa: lo
importante está cubierto, lo trivial puede no estarlo. Testear TODO al 100% es
carísimo y los tests redundantes (CRUD que solo pasa datos) aportan poco.

> [!tip] El criterio real
> No es "testear todo", es **testear lo que puede romperse y costaría caro**.
> Un bug en el login cuesta miles en daño; un bug en el CRUD de sponsors se arregla rápido.

## Código — los 4 tests de UserService

### 1. Email duplicado lanza excepción (guard clause)

```csharp
[Fact]
public async Task CreateAsync_WithDuplicateEmail_ThrowsException()
{
    _mockRepo.Setup(r => r.ExistsByEmailAsync("dupe@test.com"))
             .ReturnsAsync(true);

    var user = new User { Email = "dupe@test.com", FirstName = "A", LastName = "B", Role = UserRole.Viewer };

    var act = () => _service.CreateAsync(user, "Passw0rd");

    await Assert.ThrowsAsync<InvalidOperationException>(act);
}
```

### 2. El password se guarda hasheado, nunca en texto plano

```csharp
[Fact]
public async Task CreateAsync_WithValidData_HashesPassword()
{
    _mockRepo.Setup(r => r.ExistsByEmailAsync("new@test.com"))
             .ReturnsAsync(false);
    _mockRepo.Setup(r => r.CreateAsync(It.IsAny<User>()))
             .ReturnsAsync((User u) => u);

    var user = new User { Email = "new@test.com", FirstName = "A", LastName = "B", Role = UserRole.Viewer };

    var result = await _service.CreateAsync(user, "Passw0rd");

    Assert.NotEqual("Passw0rd", result.PasswordHash);
    Assert.True(BCrypt.Net.BCrypt.Verify("Passw0rd", result.PasswordHash));
}
```

> [!info] `ReturnsAsync((User u) => u)`
> Le dice al mock: "devolveme el mismo usuario que me diste". Así podés inspeccionar
> el resultado que pasó por el servicio. `It.IsAny<User>()` significa "cualquier objeto User".

### 3. Password equivocada → login rechazado

```csharp
[Fact]
public async Task ValidatePasswordAsync_WrongPassword_ReturnsFalse()
{
    _mockRepo.Setup(r => r.GetByEmailAsync("user@test.com"))
             .ReturnsAsync(new User { Email = "user@test.com", PasswordHash = BCrypt.Net.BCrypt.HashPassword("Correct1") });

    var result = await _service.ValidatePasswordAsync("user@test.com", "Wrong1");

    Assert.False(result);
}
```

### 4. Password correcta → login aceptado

```csharp
[Fact]
public async Task ValidatePasswordAsync_CorrectPassword_ReturnsTrue()
{
    _mockRepo.Setup(r => r.GetByEmailAsync("user@test.com"))
             .ReturnsAsync(new User { Email = "user@test.com", PasswordHash = BCrypt.Net.BCrypt.HashPassword("Correct1") });

    var result = await _service.ValidatePasswordAsync("user@test.com", "Correct1");

    Assert.True(result);
}
```

## Estructura base de la clase de tests

```csharp
public class UserServiceTests
{
    private readonly Mock<IUserRepository> _mockRepo;
    private readonly UserService _service;

    public UserServiceTests()
    {
        _mockRepo = new Mock<IUserRepository>();
        var logger = new Mock<ILogger<UserService>>();
        _service = new UserService(_mockRepo.Object, logger.Object);
    }

    // ... los tests van acá (dentro de la clase, antes de la llave final)
}
```

> [!tip] ¿Por qué el logger también es un mock?
> `UserService` recibe `ILogger<UserService>` por inyección. Si no se lo diéramos,
> el constructor fallaría. Como es una dependencia "de soporte" (no de lógica),
> se mockea con `new Mock<ILogger<UserService>>()` y se pasa `.Object` sin más.

## Cómo correr los tests

```bash
# En la raíz del proyecto (hay DOS soluciones: SportsLeague.sln y SportsLegue.slnx)
dotnet test SportsLeague.sln

# Test específico
dotnet test SportsLeague.sln --filter "FullyQualifiedName~ValidatePasswordAsync"
```

Salida esperada con los 4 tests: `total: 4, con errores: 0, correcto: 4, omitido: 0`.

## Referencia
- [[20 - PROYECTOS/DevSecOps-Proyecto/Fase-1-Testing/Fase-1-Testing]]
- [[Testing]]
- [[Seguridad del codigo]]

#testing #devsecops #unit-tests #mocks #bcrypt #guard-clauses #fase-1
