# perFin — Backlog inicial de specs V1

> Objetivo: dividir la implementación en unidades pequeñas, verificables y con dependencias explícitas.

## Orden propuesto

### 001 — Accounts

**Incluye:**
- cuentas banco/efectivo/billetera/tarjeta;
- carga inicial;
- reconstrucción básica de saldo.

**Depende de:** ninguna feature de negocio previa.

---

### 002 — Concepts

**Incluye:**
- alta/edición/desactivación de conceptos;
- tipo ingreso/egreso;
- categoría/subcategoría.

**Depende de:** ninguna feature de negocio previa.

---

### 003 — Basic movements

**Incluye:**
- ingreso real;
- egreso directo;
- impactos en cuenta;
- reglas básicas de saldo.

**Depende de:** 001, 002.

---

### 004 — Transfers

**Incluye:**
- transferencia entre cuentas propias;
- origen ≠ destino;
- impacto doble;
- no generar ingreso/gasto.

**Depende de:** 001, 003.

---

### 005 — Obligations

**Incluye:**
- regla recurrente de egreso;
- obligación manual;
- generación de obligaciones;
- monto estimado/final;
- estados derivados.

**Depende de:** 002.

---

### 006 — Obligation payments

**Incluye:**
- pago total/parcial desde banco, efectivo o billetera;
- actualización del saldo pendiente;
- invariantes de sobrepago.

**Depende de:** 001, 003, 005.

---

### 007 — Cards

**Incluye:**
- configuración de tarjeta;
- cierre inclusivo/exclusivo;
- vencimiento;
- cálculo del período según fecha y cierre.

**Depende de:** 001.

---

### 008 — Card purchases

**Incluye:**
- compra con tarjeta en una cuota;
- imputación automática al mes según cierre;
- generación de gasto/deuda;
- pago posterior no contabilizado como gasto.

**Depende de:** 002, 003, 007.

---

### 009 — Card obligation payment

**Incluye:**
- pagar obligación con tarjeta;
- cancelar deuda con proveedor;
- registrar compromiso/deuda de tarjeta sin doble gasto.

**Depende de:** 005, 006, 007, 008.

---

### 010 — Installments

**Incluye:**
- `CompraEnCuotas`;
- cantidad de cuotas;
- importe por cuota;
- cálculo derivado de meses futuros;
- fecha estimada de finalización;
- sin entidad `Cuota` persistida en V1.

**Depende de:** 002, 007.

---

### 011 — Card forecast

**Incluye:**
- consulta por tarjeta y mes;
- monto estimado a pagar;
- detalle de compromisos futuros;
- cuotas restantes y finalización.

**Depende de:** 007, 008, 010.

---

### 012 — Card charges

**Incluye:**
- intereses;
- impuestos;
- comisiones;
- otros cargos variables;
- imputación al período correspondiente.

**Depende de:** 002, 007, 008.

---

### 013 — Card payments

**Incluye:**
- pago total/parcial de tarjeta desde cuenta de activo;
- reducción de deuda;
- no generar gasto.

**Depende de:** 001, 003, 007.

---

### 014 — Refunds

**Incluye:**
- devolución/reintegro explícito;
- vínculo opcional con movimiento original;
- acreditación en cuenta elegida por usuario;
- cuenta receptora diferente de la original;
- no tratar reintegro como ingreso ordinario.

**Depende de:** 001, 002, 003.

---

### 015 — Queries and dashboard

**Incluye:**
- saldos por cuenta;
- obligaciones pendientes/vencidas;
- gastos por período/concepto;
- ingresos por período/concepto;
- resumen accionable y simple.

**Depende de:** features de negocio previas necesarias para cada consulta.

---

### 016 — Imports

**Incluye:**
- importación de archivo compatible;
- persistencia de movimientos importados pendientes;
- deduplicación inicial;
- sin impacto financiero antes de confirmación.

**Depende de:** 001, 003.

---

### 017 — Reconciliation

**Incluye:**
- reglas de conciliación;
- sugerencias;
- confirmar/corregir/ignorar;
- conversión a operación normal del dominio.

**Depende de:** 002, 003, 005, 007, 016 según clasificación.

---

### 018 — Operation corrections

**Incluye:**
- política de corrección;
- anulación/reemplazo;
- trazabilidad;
- recalcular efectos sin corrupción de datos.

**Depende de:** modelo estabilizado de movimientos.

## Specs transversales

Además de las specs de negocio se requerirán specs o planes técnicos pequeños para:

- bootstrap del monorepo;
- Auth.Api + Identity + JWT/refresh;
- Business.Api base;
- persistencia EF Core/PostgreSQL;
- Angular shell + auth integration;
- OpenAPI/Problem Details;
- observabilidad y health checks;
- Docker Compose;
- GitHub Actions;
- estrategia de backups/despliegue.

Estas specs técnicas no pueden modificar semántica del dominio.