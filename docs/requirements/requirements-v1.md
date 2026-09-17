# perFin — Requisitos V1

> Estado: borrador depurado a partir del dominio acordado.

## 1. Requisitos funcionales

### Cuentas y carga inicial

- **RF-01** El sistema debe permitir administrar cuentas de tipo banco, efectivo, billetera virtual y tarjeta de crédito.
- **RF-02** Debe permitir registrar una carga inicial para una cuenta de activo o una deuda inicial de tarjeta.
- **RF-03** La carga inicial no debe contabilizarse como ingreso ni gasto.
- **RF-04** El historial y los efectos de una cuenta deben poder reconstruirse desde cargas iniciales y movimientos posteriores.

### Conceptos

- **RF-05** El sistema debe permitir administrar conceptos de ingreso y egreso.
- **RF-06** Los conceptos deben poder agruparse por categoría y opcionalmente subcategoría.
- **RF-07** La recurrencia no debe formar parte del concepto.

### Ingresos

- **RF-08** El sistema debe permitir registrar ingresos reales cuando ocurren.
- **RF-09** Todo ingreso debe poder clasificarse por concepto, por ejemplo sueldo o trabajo freelance.
- **RF-10** V1 no debe implementar ingresos esperados ni planificación de ingresos.

### Egresos y transferencias

- **RF-11** El sistema debe permitir registrar egresos directos.
- **RF-12** Debe permitir transferencias entre cuentas propias.
- **RF-13** Una transferencia entre cuentas propias no debe contabilizarse como ingreso ni gasto.
- **RF-14** Una transferencia no puede tener la misma cuenta como origen y destino.

### Reglas recurrentes y obligaciones

- **RF-15** El sistema debe permitir definir reglas recurrentes de egreso.
- **RF-16** Una regla recurrente debe generar obligaciones concretas para los períodos correspondientes.
- **RF-17** Debe ser posible crear obligaciones manuales no originadas por una regla.
- **RF-18** Una obligación debe soportar monto estimado y monto final cuando corresponda.
- **RF-19** Debe ser posible pagar una obligación total o parcialmente.
- **RF-20** El estado de una obligación debe derivarse de monto, pagos y vencimiento.
- **RF-21** Los estados funcionales deben contemplar pendiente, parcial, pagada y vencida.
- **RF-21A** El sistema debe permitir consultar claramente qué gastos comprometidos fueron pagados y cuáles permanecen pendientes de pago.

### Tarjetas

- **RF-22** Una tarjeta debe configurarse con día de cierre, criterio de inclusión del día de cierre y día de vencimiento.
- **RF-23** El sistema debe determinar automáticamente el período de una compra con tarjeta según fecha de compra y cierre.
- **RF-24** El usuario no debe seleccionar manualmente el período normal de una compra con tarjeta.
- **RF-25** Una compra con tarjeta debe contabilizarse como gasto en el mes determinado por el cierre.
- **RF-26** El posterior pago de tarjeta no debe generar un nuevo gasto.
- **RF-27** Debe ser posible pagar total o parcialmente deuda de tarjeta desde una cuenta de activo.

### Compras en cuotas

- **RF-28** El sistema debe permitir registrar compras en cuotas con tarjeta.
- **RF-29** Una compra en cuotas debe registrar tarjeta, fecha, concepto, cantidad de cuotas e importe por cuota.
- **RF-30** No debe reconocerse el importe total de una compra en cuotas como gasto inicial.
- **RF-31** El sistema debe calcular estimativamente cuánto corresponde pagar por esa compra en cada mes futuro según el cierre de la tarjeta.
- **RF-32** Debe permitir conocer cuántas cuotas restan y en qué mes finaliza el compromiso.
- **RF-33** V1 no debe requerir una entidad persistida por cada cuota si la información puede derivarse de la compra.

### Cargos de tarjeta

- **RF-34** Intereses, impuestos, comisiones y cargos similares deben registrarse como nuevos gastos variables.
- **RF-35** Estos cargos deben afectar el período de tarjeta correspondiente.
- **RF-36** No deben interpretarse como pagos ni ajustes de la compra original.

### Devoluciones y reintegros

- **RF-37** El sistema debe permitir registrar una devolución/reintegro como operación explícita.
- **RF-38** Cuando exista, debe poder relacionarse con la operación original.
- **RF-39** El usuario debe indicar la cuenta donde el reintegro fue efectivamente acreditado.
- **RF-40** La cuenta de reintegro puede ser diferente de la utilizada en la compra original.
- **RF-41** Un reintegro no debe clasificarse como ingreso ordinario.

### Importaciones y conciliación

- **RF-42** El sistema debe permitir importar movimientos provenientes de extractos bancarios o fuentes compatibles.
- **RF-43** Un movimiento importado no confirmado no debe afectar saldos ni gastos.
- **RF-44** El sistema debe detectar potenciales duplicados en importaciones.
- **RF-45** Debe poder sugerir clasificación para un movimiento importado.
- **RF-46** El usuario debe poder confirmar, corregir o ignorar la sugerencia.
- **RF-47** Al confirmar un movimiento importado, debe producirse una operación normal del dominio.
- **RF-48** Debe permitir administrar reglas de conciliación basadas en patrones.
- **RF-49** Una regla de conciliación sólo puede sugerir operaciones válidas según las invariantes del dominio.

### Consultas

- **RF-50** El sistema debe mostrar gastos comprometidos distinguiendo pagados, pendientes, parciales y vencidos.
- **RF-51** Debe mostrar obligaciones pendientes y vencimientos próximos.
- **RF-52** Debe permitir consultar gastos por período y concepto.
- **RF-53** Debe permitir consultar ingresos por período y concepto.
- **RF-54** Debe permitir consultar, por tarjeta y mes, el importe estimado a pagar.
- **RF-55** Debe mostrar compromisos futuros de compras en cuotas y su fecha estimada de finalización.

## 2. Requisitos no funcionales

- **RNF-01** La operación cotidiana debe requerir menos esfuerzo que el método manual que reemplaza.
- **RNF-02** Cada pantalla o flujo debe mostrar sólo información pertinente a la acción actual.
- **RNF-03** El usuario no debe trabajar con identificadores técnicos internos.
- **RNF-04** Las reglas financieras deben residir en el dominio, no dispersas entre UI, controladores o infraestructura.
- **RNF-05** El dominio debe permanecer aislado de tecnologías externas.
- **RNF-06** Los datos financieros deben poder respaldarse y restaurarse.
- **RNF-07** Los saldos deben ser reconstruibles desde operaciones persistidas.
- **RNF-08** Las operaciones financieras deben mantener trazabilidad suficiente para explicar sus efectos.
- **RNF-09** La importación debe evitar duplicación accidental en la medida técnicamente razonable.
- **RNF-10** La interfaz debe minimizar decisiones repetitivas mediante cálculos y valores predeterminados derivados del dominio.
- **RNF-11** El sistema debe utilizar `decimal` para importes monetarios.
- **RNF-12** Las fechas financieras sin componente horario deben modelarse como fecha, no timestamp.
- **RNF-13** Los secretos no deben almacenarse en el repositorio.
- **RNF-14** Una feature no se considera completada si falla build, tests aplicables o CI.
- **RNF-15** Nuevas dependencias externas requieren aprobación explícita en spec o plan técnico.

## 3. Restricciones tecnológicas

- .NET 10.
- ASP.NET Core Web API.
- EF Core 10.
- PostgreSQL 17.
- Npgsql compatible con EF Core 10.
- Arquitectura hexagonal.
- Auth API separada de Business API.
- ASP.NET Core Identity para gestión de identidad.
- JWT Bearer para autenticación.
- Angular 22 con standalone components.
- Bootstrap 5.
- REST + JSON + OpenAPI.
- Problem Details para errores HTTP.
- xUnit, Testcontainers y WebApplicationFactory para backend.
- Playwright para flujos E2E críticos.
- Docker / Docker Compose.
- GitHub Actions.

## 4. Fuera de alcance

V1 no incluye:

- ingresos esperados;
- presupuestos mensuales;
- objetivos de ahorro;
- inversiones;
- multi-moneda;
- préstamos complejos;
- predicción financiera;
- sincronización bancaria en tiempo real.