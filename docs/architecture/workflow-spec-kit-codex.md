# perFin — Flujo de trabajo con GitHub Spec Kit y Codex

## 1. Objetivo

La implementación será realizada con Codex como agente y GitHub Spec Kit como marco de especificación y ejecución.

La regla principal es que Codex implementa decisiones ya tomadas; no diseña el producto por iniciativa propia.

## 2. Flujo general

```text
Decisión de producto/dominio
        ↓
Documentación maestra
        ↓
Spec pequeña
        ↓
Clarificación
        ↓
Plan técnico
        ↓
Tasks
        ↓
Análisis de consistencia
        ↓
Implementación con Codex
        ↓
Tests + CI
        ↓
PR
        ↓
Merge a main
```

## 3. Reglas para specs

Cada spec debe:

- resolver una capacidad pequeña y coherente;
- indicar qué casos de uso cubre;
- indicar qué requisitos satisface;
- indicar qué entidades/reglas del dominio utiliza;
- declarar explícitamente lo que está fuera de alcance;
- evitar decisiones de producto implícitas;
- incluir criterios de aceptación verificables.

## 4. Restricciones para Codex

Codex no puede:

- agregar entidades de dominio no aprobadas;
- agregar estados financieros no aprobados;
- modificar semántica de ingresos/gastos;
- cambiar reglas de tarjeta o cuotas;
- introducir paquetes NuGet/npm no aprobados;
- mezclar Auth.Api y Business.Api;
- introducir dependencias tecnológicas en Domain;
- modificar el DER por conveniencia técnica sin decisión previa;
- considerar terminada una task con build/tests/CI fallando.

Si encuentra una ambigüedad funcional, debe devolverse al proceso de especificación.

## 5. Estrategia de branches

- `main`: estable e integrable.
- una branch por spec/feature.
- cambios mediante Pull Request.
- CI requerido antes de merge.

Convención sugerida:

```text
spec/001-accounts
spec/002-concepts
spec/003-basic-movements
...
```

La convención definitiva puede adaptarse a la estructura generada por Spec Kit.

## 6. Monorepo

Se utilizará un único repositorio:

```text
perFin/
├── docs/
├── specs/
├── .specify/
├── src/
│   ├── backend/
│   └── frontend/
├── tests/
├── deploy/
└── .github/workflows/
```

Auth.Api y Business.Api son deployables separados, pero permanecen en el mismo repositorio.

## 7. Definition of Done mínima

Una feature sólo puede considerarse terminada si:

- satisface los criterios de aceptación;
- no viola la constitución del proyecto;
- no introduce comportamiento fuera de spec;
- compila;
- pasan unit tests aplicables;
- pasan integration tests aplicables;
- pasan E2E si el flujo es crítico;
- el pipeline de GitHub Actions está verde;
- la documentación afectada permanece coherente.

## 8. Cambio de dominio

Si una feature descubre una necesidad real no contemplada:

1. detener implementación del punto ambiguo;
2. documentar la necesidad;
3. actualizar modelo de dominio/requisitos/DER si se aprueba;
4. ajustar la spec;
5. continuar implementación.

No se permite resolver silenciosamente la ambigüedad en código.