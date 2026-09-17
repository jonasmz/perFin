# perFin — Modelo entidad-relación conceptual V1

> Estado: borrador conceptual. No representa todavía el esquema físico final de PostgreSQL.

## 1. Objetivo

Este DER conceptual traduce el modelo de dominio V1 a entidades persistentes y relaciones principales. Su propósito es restringir la implementación, no definir todavía detalles de índices, tipos físicos o migraciones.

## 2. Entidades principales

```text
Cuenta
Tarjeta
Concepto
Movimiento
ImpactoCuenta
ReglaRecurrente
Obligacion
CompraEnCuotas
Importacion
MovimientoImportado
ReglaConciliacion
```

## 3. Relaciones

```text
Cuenta 1 ───── 0..1 Tarjeta

Cuenta 1 ───── N ImpactoCuenta
Movimiento 1 ─ N ImpactoCuenta

Concepto 1 ─── N Movimiento
Concepto 1 ─── N ReglaRecurrente
Concepto 1 ─── N Obligacion
Concepto 1 ─── N CompraEnCuotas

ReglaRecurrente 1 ─── N Obligacion

Tarjeta 1 ─── N CompraEnCuotas

Movimiento 1 ─── 0..N Movimiento
     relación opcional para devolución/reintegro u otras relaciones explícitas aprobadas

Importacion 1 ─── N MovimientoImportado

ReglaConciliacion N ─── 0..1 Concepto
ReglaConciliacion N ─── 0..1 Cuenta

MovimientoImportado 0..1 ─── 0..1 Movimiento
     cuando se confirma una importación
```

## 4. Diagrama conceptual

```text
                              ┌──────────────┐
                              │   Concepto   │
                              └──────┬───────┘
                     ┌───────────────┼────────────────┐
                     │               │                │
                     ▼               ▼                ▼
          ┌─────────────────┐  ┌──────────────┐  ┌────────────────┐
          │ReglaRecurrente  │  │  Movimiento  │  │CompraEnCuotas │
          └────────┬────────┘  └──────┬───────┘  └───────┬────────┘
                   │                  │                  │
                   ▼                  ▼                  ▼
             ┌────────────┐    ┌──────────────┐      ┌─────────┐
             │ Obligacion │    │ImpactoCuenta │      │ Tarjeta │
             └────────────┘    └──────┬───────┘      └────┬────┘
                                     │                   │
                                     ▼                   ▼
                                  ┌───────┐          ┌───────┐
                                  │Cuenta │◄─────────│Cuenta │
                                  └───────┘   1:0..1 └───────┘


Importacion
    │ 1
    ▼ N
MovimientoImportado
    │ 0..1
    ▼
Movimiento

ReglaConciliacion
    ├── Concepto [opcional]
    └── Cuenta   [opcional]
```

## 5. Entidad Cuenta

Campos conceptuales:

- Id
- Nombre
- Tipo
- Activa

Restricciones:

- `Tipo` ∈ BANCO, EFECTIVO, BILLETERA, TARJETA_CREDITO.
- Sólo una cuenta `TARJETA_CREDITO` puede tener una entidad `Tarjeta` asociada.

## 6. Entidad Tarjeta

Campos conceptuales:

- Id
- CuentaId
- DiaCierre
- CierreInclusivo
- DiaVencimiento
- CuentaPagoPredeterminadaId opcional

Restricciones:

- relación uno a uno con una `Cuenta` de tipo `TARJETA_CREDITO`;
- `CuentaPagoPredeterminadaId`, si existe, debe referenciar una cuenta de activo.

## 7. Entidad Concepto

Campos conceptuales:

- Id
- Nombre
- Tipo
- Categoria
- Subcategoria opcional
- Activo

Restricciones:

- `Tipo` ∈ INGRESO, EGRESO.

## 8. Entidad Movimiento

Campos conceptuales:

- Id
- Fecha
- TipoOperacion
- ConceptoId opcional según el tipo
- Importe
- Descripcion opcional
- MovimientoRelacionadoId opcional
- FechaCreacion

`MovimientoRelacionadoId` permite vincular una devolución/reintegro a la operación original sin crear una entidad adicional sólo para ese vínculo.

## 9. Entidad ImpactoCuenta

Campos conceptuales:

- Id
- MovimientoId
- CuentaId
- Variacion

Un movimiento posee uno o más impactos según el tipo de operación.

Esta entidad permite reconstruir saldos sin almacenar fórmulas específicas por tipo de movimiento.

## 10. Entidad ReglaRecurrente

Campos conceptuales:

- Id
- ConceptoId
- Frecuencia
- FechaInicio
- FechaFin opcional
- TipoMonto
- MontoBase opcional
- DiaVencimiento
- Activa

Genera obligaciones de egreso. No se usa para ingresos.

## 11. Entidad Obligacion

Campos conceptuales:

- Id
- ConceptoId
- ReglaRecurrenteId opcional
- Periodo
- MontoEstimado opcional
- MontoFinal opcional
- FechaVencimiento

El estado se considera derivado en el dominio y no necesariamente un dato físico mutable.

Los pagos asociados deberán quedar vinculados de manera explícita en el modelo lógico. La forma exacta de esa asociación se definirá al pasar del DER conceptual al lógico, evitando introducir una entidad innecesaria si puede resolverse con una relación clara desde `Movimiento`.

## 12. Entidad CompraEnCuotas

Campos conceptuales:

- Id
- ConceptoId
- TarjetaId
- FechaCompra
- CantidadCuotas
- ImporteCuota
- Descripcion opcional

No existe entidad `Cuota` en V1.

Los compromisos mensuales se calculan a partir de esta entidad y de la configuración de la tarjeta.

## 13. Entidad Importacion

Campos conceptuales:

- Id
- Fuente
- FechaImportacion
- IdentificadorArchivo opcional

## 14. Entidad MovimientoImportado

Campos conceptuales:

- Id
- ImportacionId
- Fecha
- DescripcionOriginal
- Importe
- Estado
- ClasificacionSugerida
- MovimientoConfirmadoId opcional

No altera saldos antes de ser confirmado.

## 15. Entidad ReglaConciliacion

Campos conceptuales:

- Id
- Patron
- Fuente opcional
- CuentaId opcional
- TipoOperacionSugerido
- ConceptoId opcional
- Activa
- Prioridad

Las reglas generan sugerencias, no operaciones financieras por sí mismas.

## 16. Decisiones deliberadamente no incluidas

No aparecen como entidades en V1:

- IngresoEsperado
- Presupuesto
- ObjetivoAhorro
- Cuota
- CicloTarjeta persistido como entidad obligatoria

El período estimado de una compra con tarjeta se puede calcular desde la configuración de `Tarjeta`; si más adelante existe una necesidad real de persistir ciclos, deberá aprobarse mediante una nueva decisión de dominio.

## 17. Próximo paso

Antes de crear el modelo lógico se debe validar este DER contra los casos de uso principales y definir las relaciones exactas necesarias para pagos parciales de obligaciones y confirmación de importaciones.