# perFin — Matriz de trazabilidad V1

> Estado: borrador inicial. Se actualizará a medida que cada spec quede aprobada.

## 1. Propósito

La matriz conecta casos de uso, requisitos, entidades/reglas del dominio y futuras specs de implementación.

## 2. Matriz

| Caso de uso | Requisitos principales | Dominio involucrado | Spec prevista |
|---|---|---|---|
| UC-01 Configurar cuenta | RF-01, RF-04 | Cuenta | 001-accounts |
| UC-02 Registrar carga inicial | RF-02, RF-03, RF-04 | Cuenta, Movimiento, ImpactoCuenta | 001-accounts |
| UC-03 Crear concepto | RF-05, RF-06, RF-07 | Concepto | 002-concepts |
| UC-04 Registrar ingreso | RF-08, RF-09, RF-10 | Movimiento, Concepto, Cuenta, ImpactoCuenta | 003-basic-movements |
| UC-05 Registrar egreso directo | RF-11 | Movimiento, Concepto, Cuenta, ImpactoCuenta | 003-basic-movements |
| UC-06 Transferir entre cuentas | RF-12, RF-13, RF-14 | Movimiento, Cuenta, ImpactoCuenta | 004-transfers |
| UC-07 Crear regla recurrente | RF-15, RF-16 | ReglaRecurrente, Concepto | 005-obligations |
| UC-08 Crear obligación manual | RF-17, RF-18 | Obligacion, Concepto | 005-obligations |
| UC-09 Pagar obligación desde activo | RF-19, RF-20, RF-21 | Obligacion, Movimiento, Cuenta, ImpactoCuenta | 006-obligation-payments |
| UC-10 Pagar obligación con tarjeta | RF-19, RF-22, RF-23, RF-25 | Obligacion, Tarjeta, Movimiento | 009-card-obligation-payment |
| UC-11 Configurar tarjeta | RF-22, RF-23, RF-24 | Cuenta, Tarjeta | 007-cards |
| UC-12 Compra tarjeta 1 cuota | RF-23, RF-24, RF-25, RF-26 | Tarjeta, Movimiento, Concepto | 008-card-purchases |
| UC-13 Compra en cuotas | RF-28 a RF-33 | CompraEnCuotas, Tarjeta, Concepto | 010-installments |
| UC-14 Consultar compromisos futuros | RF-31, RF-32, RF-54, RF-55 | CompraEnCuotas, Tarjeta | 011-card-forecast |
| UC-15 Registrar cargo tarjeta | RF-34, RF-35, RF-36 | Movimiento, Tarjeta, Concepto | 012-card-charges |
| UC-16 Pagar tarjeta | RF-26, RF-27 | Movimiento, Cuenta, Tarjeta, ImpactoCuenta | 013-card-payments |
| UC-17 Devolución/reintegro | RF-37 a RF-41 | Movimiento, Cuenta, Concepto | 014-refunds |
| UC-18 Consultar saldos | RF-50 | Cuenta, ImpactoCuenta | 015-queries-dashboard |
| UC-19 Consultar obligaciones | RF-51 | Obligacion | 015-queries-dashboard |
| UC-20 Consultar gastos | RF-52 | Movimiento, Concepto | 015-queries-dashboard |
| UC-21 Consultar ingresos | RF-53 | Movimiento, Concepto | 015-queries-dashboard |
| UC-22 Importar extracto | RF-42, RF-43, RF-44 | Importacion, MovimientoImportado | 016-imports |
| UC-23 Sugerir clasificación | RF-45, RF-49 | MovimientoImportado, ReglaConciliacion | 017-reconciliation |
| UC-24 Revisar importado | RF-46, RF-47 | MovimientoImportado, Movimiento | 017-reconciliation |
| UC-25 Crear regla conciliación | RF-48, RF-49 | ReglaConciliacion | 017-reconciliation |
| UC-26 Corregir operación | trazabilidad y consistencia | Movimiento y entidades relacionadas | 018-operation-corrections |
| UC-27 Anular operación | trazabilidad y consistencia | Movimiento y entidades relacionadas | 018-operation-corrections |

## 3. Reglas de mantenimiento

Cuando se cree una spec real:

1. reemplazar el nombre previsto por la ruta/identificador real generado por Spec Kit;
2. verificar que todos sus requisitos tengan caso de uso o justificación explícita;
3. verificar que toda entidad nueva haya sido aprobada en el modelo de dominio;
4. agregar criterios de aceptación trazables a los requisitos correspondientes;
5. no marcar una fila como implementada sólo porque existe código: debe cumplir tests y CI.