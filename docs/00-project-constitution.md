# perFin — Constitución del proyecto

## 1. Propósito

perFin es un sistema de finanzas personales orientado a reducir el esfuerzo operativo del usuario. La complejidad necesaria para representar movimientos, obligaciones, tarjetas, cuotas e importaciones debe permanecer dentro del software y no trasladarse a la interfaz.

## 2. Principios obligatorios

### 2.1 Simplicidad para el usuario

La operación cotidiana debe requerir menos esfuerzo que el método manual que reemplaza. Cada flujo debe solicitar únicamente información pertinente a la operación actual.

### 2.2 Estados inválidos deben ser imposibles

El sistema no debe permitir combinaciones sin sentido y luego depender de validaciones posteriores para detectarlas. Ejemplos:

- un pago en efectivo no puede pertenecer a un ciclo de tarjeta;
- una transferencia no puede tener la misma cuenta de origen y destino;
- un pago de tarjeta debe involucrar una cuenta de tarjeta;
- una compra con tarjeta no permite elegir manualmente su ciclo normal.

### 2.3 Dominio aislado de tecnologías

El proyecto adopta arquitectura hexagonal. El dominio financiero no debe depender de ASP.NET Core, Entity Framework Core, PostgreSQL, Identity, JWT, HTTP, JSON, Angular ni otras tecnologías de infraestructura.

### 2.4 No implementar funcionalidad especulativa

Codex no puede introducir entidades, reglas financieras, estados, paquetes o comportamientos que no estén aprobados por una spec o por una decisión arquitectónica explícita.

Si una implementación requiere una nueva decisión de producto o dominio, debe detenerse y volver al proceso de especificación.

### 2.5 Especificaciones pequeñas

El sistema se implementará mediante specs pequeñas, cohesivas y revisables. No se utilizará una única mega-spec para toda la aplicación.

### 2.6 Fuente de verdad financiera

Los efectos financieros deben poder explicarse y reconstruirse desde operaciones registradas. No deben existir saldos paralelos mantenidos manualmente como segunda fuente de verdad.

### 2.7 Contabilización única

Una misma operación económica no puede reconocerse como gasto más de una vez.

En particular:

- una transferencia entre cuentas propias no es gasto;
- pagar una tarjeta no es gasto;
- los cargos, compras o cuotas que originan la deuda sí son gastos según las reglas del dominio.

### 2.8 Trazabilidad

Toda funcionalidad debe poder vincularse con:

1. un caso de uso;
2. uno o más requisitos;
3. entidades del dominio;
4. reglas e invariantes aplicables;
5. una spec de implementación.

### 2.9 Dependencias restringidas

Codex no puede agregar paquetes NuGet o npm sin que hayan sido aprobados en la spec o en el plan técnico correspondiente.

No se incorporarán librerías por conveniencia si .NET o Angular ya cubren adecuadamente la necesidad.

### 2.10 Calidad obligatoria

Una feature no se considera terminada mientras no compile, sus tests aplicables no pasen y el pipeline de CI no esté verde.

## 3. Reglas técnicas globales

- Backend: .NET 10.
- ORM: Entity Framework Core 10.
- Base de datos: PostgreSQL 17.
- Provider: Npgsql compatible con EF Core 10.
- Arquitectura: hexagonal.
- Auth: ASP.NET Core Identity en API independiente.
- Autenticación entre cliente y APIs: JWT Bearer.
- Frontend: Angular 22 con standalone components.
- UI: Bootstrap 5.
- Contratos: REST + JSON + OpenAPI.
- Errores HTTP: Problem Details.
- Tests backend: xUnit.
- Integración: PostgreSQL real mediante Testcontainers y WebApplicationFactory.
- E2E: Playwright.
- CI: GitHub Actions.
- Contenedores: Docker / Docker Compose.
- Observabilidad: ILogger + OpenTelemetry.

## 4. Datos financieros

- Importes monetarios: `decimal`; nunca `float` ni `double`.
- Persistencia monetaria: `numeric(18,2)` salvo decisión posterior explícita.
- Fechas sin hora: `DateOnly` cuando corresponda.
- Auditoría y timestamps técnicos: UTC mediante `DateTimeOffset`.

## 5. Regla de evolución

Este documento tiene prioridad sobre decisiones implícitas de implementación. Una spec puede agregar detalles, pero no contradecir estos principios sin modificar explícitamente esta constitución.