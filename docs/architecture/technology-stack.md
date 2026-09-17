# perFin — Stack tecnológico

## Backend

- .NET 10
- ASP.NET Core Web API
- Entity Framework Core 10
- PostgreSQL 17
- Npgsql compatible con EF Core 10
- Arquitectura hexagonal

## Autenticación

- API de autenticación separada de la API de negocio.
- ASP.NET Core Identity para usuarios, password hashing, roles/claims y seguridad de identidad.
- JWT Bearer para autenticar requests hacia las APIs.
- Access tokens de vida corta.
- Refresh tokens rotatorios.
- Refresh tokens persistidos de forma segura y nunca en texto plano.

La API de negocio no accede a contraseñas ni tablas internas de Identity. Consume únicamente la identidad/claims validados desde el JWT.

## Frontend

- Angular 22
- Standalone components
- TypeScript
- Bootstrap 5
- Arquitectura por features
- Lazy loading por feature cuando corresponda
- Angular signals para estado local cuando sea suficiente
- HttpClient
- Guards e interceptors funcionales

No se incorporan stores globales como NgRx salvo necesidad aprobada posteriormente.

## Contratos

- REST
- JSON
- HTTPS
- OpenAPI
- Problem Details para errores HTTP

## Testing

### Backend

- xUnit
- Unit tests para Domain y Application
- Integration tests con WebApplicationFactory
- PostgreSQL real mediante Testcontainers

No utilizar EF Core InMemory para validar comportamiento específico de persistencia PostgreSQL.

### Frontend

- tests unitarios/componentes según necesidad
- Playwright para flujos E2E críticos

## Observabilidad

- Microsoft.Extensions.Logging / ILogger
- logging estructurado
- OpenTelemetry para trazas y métricas
- endpoints de health/liveness/readiness

## Persistencia y datos

- Migraciones con EF Core
- dinero con `decimal`
- PostgreSQL `numeric(18,2)` salvo decisión explícita posterior
- `DateOnly` para fechas financieras sin hora
- `DateTimeOffset` UTC para timestamps técnicos

## Infraestructura de desarrollo

- Docker
- Docker Compose
- GitHub Actions
- GitHub como repositorio único

## Gestión de secretos

Ningún secreto real se almacena en Git.

Desarrollo:

- `dotnet user-secrets` y/o variables de entorno
- `.env` sólo si está explícitamente ignorado por Git

Despliegue:

- variables de entorno o secret store del entorno elegido

Incluye claves de firma JWT y credenciales de base de datos.

## Política de dependencias

Codex no puede agregar paquetes NuGet o npm por conveniencia. Toda dependencia nueva debe estar aprobada por la spec o por el plan técnico de la feature correspondiente.