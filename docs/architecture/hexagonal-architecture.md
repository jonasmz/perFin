# perFin — Arquitectura hexagonal

## 1. Objetivo

Mantener el dominio financiero aislado de frameworks, persistencia, transporte HTTP, autenticación e interfaz.

## 2. Proyectos backend previstos

```text
src/backend/
├── PerFin.Domain/
├── PerFin.Application/
├── PerFin.Infrastructure/
├── PerFin.Business.Api/
├── PerFin.Auth.Api/
└── PerFin.Auth.Infrastructure/
```

Los nombres definitivos pueden ajustarse durante el bootstrap sin alterar las fronteras.

## 3. Responsabilidades

### Domain

Contiene:

- entidades;
- value objects si se aprueban;
- reglas e invariantes financieras;
- servicios de dominio cuando sean necesarios;
- contratos puramente de dominio.

No referencia:

- EF Core;
- ASP.NET Core;
- Npgsql;
- PostgreSQL;
- Identity;
- JWT;
- HTTP;
- JSON;
- logging concreto;
- Angular.

### Application

Contiene casos de aplicación y orquestación:

- comandos/consultas según la forma aprobada en cada spec;
- puertos requeridos por el dominio/aplicación;
- autorización contextual de casos de uso cuando corresponda;
- validación de entrada que no sea una invariante financiera.

Depende de Domain.

### Infrastructure

Implementa adaptadores externos:

- EF Core;
- PostgreSQL/Npgsql;
- repositorios/puertos aprobados;
- importación de archivos;
- observabilidad/integraciones técnicas.

Puede depender de Application y Domain. El sentido inverso está prohibido.

### Business.Api

Adaptador HTTP para el dominio financiero.

Responsabilidades:

- endpoints;
- autenticación JWT Bearer;
- binding/serialización;
- Problem Details;
- OpenAPI;
- composición de dependencias.

No contiene reglas financieras.

### Auth.Api

Servicio independiente de identidad.

Responsabilidades:

- registro/login según specs aprobadas;
- ASP.NET Core Identity;
- emisión/renovación/revocación de tokens;
- gestión de credenciales.

No contiene lógica financiera.

## 4. Regla de dependencias

```text
Domain
  ↑
Application
  ↑
Infrastructure
  ↑
Business.Api
```

La flecha indica que las capas externas conocen las internas; las internas no conocen las externas.

Auth constituye un subsistema separado y no debe filtrarse dentro del dominio financiero.

## 5. Frontend

Estructura conceptual:

```text
src/frontend/perfin-web/src/app/
├── core/
│   ├── auth/
│   ├── http/
│   └── guards/
├── shared/
└── features/
    ├── accounts/
    ├── concepts/
    ├── movements/
    ├── obligations/
    ├── cards/
    ├── installments/
    └── imports/
```

Se utilizarán standalone components y rutas por feature.

El frontend no duplica reglas financieras. Debe consumir resultados/validaciones del backend y sólo aplicar validaciones de experiencia de usuario que no sustituyan al dominio.

## 6. Regla de implementación

Una conveniencia técnica no autoriza a romper las fronteras. Si una feature parece requerir dependencias del dominio hacia infraestructura, debe revisarse el diseño antes de continuar.