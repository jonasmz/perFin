# perFin — Casos de uso V1

> Estado: borrador depurado.

## UC-01 Configurar cuenta

**Objetivo:** crear una cuenta utilizable por el sistema.

**Precondiciones:** usuario autenticado.

**Datos:** nombre, tipo y estado activo.

**Resultado:** cuenta disponible para operaciones.

**Reglas:** una cuenta de tarjeta requiere configuración específica de `Tarjeta`.

---

## UC-02 Registrar carga inicial

**Objetivo:** establecer el punto de partida de una cuenta.

**Datos:** cuenta, importe y fecha.

**Resultado:** saldo inicial de activo o deuda inicial de tarjeta.

**Reglas:** no genera ingreso ni gasto.

---

## UC-03 Crear concepto

**Objetivo:** crear una clasificación económica reutilizable.

**Datos:** nombre, tipo ingreso/egreso, categoría y subcategoría opcional.

**Resultado:** concepto disponible para operaciones.

---

## UC-04 Registrar ingreso

**Objetivo:** registrar dinero efectivamente recibido.

**Datos:** fecha, cuenta receptora, importe, concepto y descripción opcional.

**Resultado:** aumenta el saldo de la cuenta y queda clasificado como ingreso.

**Reglas:** no existe planificación de ingresos esperados.

---

## UC-05 Registrar egreso directo

**Objetivo:** registrar un gasto que no necesita obligación previa.

**Datos:** fecha, cuenta, importe, concepto y descripción opcional.

**Resultado:** disminuye la cuenta y se registra gasto del período aplicable.

---

## UC-06 Transferir entre cuentas propias

**Objetivo:** mover dinero entre dos cuentas propias.

**Datos:** fecha, origen, destino, importe.

**Resultado:** disminuye origen y aumenta destino.

**Reglas:** origen y destino deben ser distintos; no genera ingreso ni gasto.

---

## UC-07 Crear regla recurrente

**Objetivo:** representar un compromiso periódico de egreso.

**Datos:** concepto, frecuencia, vigencia, monto base/tipo de monto y vencimiento.

**Resultado:** regla activa capaz de generar obligaciones.

---

## UC-08 Crear obligación manual

**Objetivo:** registrar un compromiso no recurrente.

**Datos:** concepto, período, monto estimado/final y vencimiento.

**Resultado:** obligación pendiente disponible para seguimiento y pago.

---

## UC-09 Pagar obligación desde cuenta de activo

**Objetivo:** cancelar total o parcialmente una obligación desde banco, efectivo o billetera.

**Datos:** obligación, cuenta, importe y fecha.

**Resultado:** disminuye la cuenta y se reduce el saldo pendiente de la obligación.

**Reglas:** el pago no puede exceder el saldo admitido por la regla de dominio definida para pagos parciales.

---

## UC-10 Pagar obligación con tarjeta

**Objetivo:** satisfacer una obligación utilizando tarjeta de crédito.

**Datos:** obligación, tarjeta, importe y fecha.

**Resultado:** se reduce la deuda con el proveedor y se genera deuda/cargo de tarjeta en el período determinado por cierre.

**Reglas:** el posterior pago de tarjeta no vuelve a generar gasto.

---

## UC-11 Configurar tarjeta

**Objetivo:** definir comportamiento de una cuenta de tarjeta.

**Datos:** cuenta, día de cierre, cierre inclusivo/exclusivo, día de vencimiento y cuenta de pago predeterminada opcional.

**Resultado:** tarjeta preparada para calcular períodos automáticamente.

---

## UC-12 Registrar compra con tarjeta en una cuota

**Objetivo:** registrar un gasto financiado hasta el pago del resumen.

**Datos:** fecha, tarjeta, concepto, importe y descripción opcional.

**Resultado:** gasto asignado al mes determinado por cierre y deuda de tarjeta correspondiente.

**Reglas:** el usuario no elige manualmente el período.

---

## UC-13 Registrar compra en cuotas

**Objetivo:** registrar un compromiso futuro en cuotas.

**Datos:** fecha de compra, tarjeta, concepto, cantidad de cuotas, importe por cuota y descripción opcional.

**Resultado:** el sistema puede calcular el importe estimado comprometido para cada mes futuro y cuándo finaliza la compra.

**Reglas:** no se reconoce el total de la compra como gasto inicial; no se requiere persistir una entidad por cuota en V1.

---

## UC-14 Consultar compromisos futuros de tarjeta

**Objetivo:** saber cuánto dinero se estima pagar en una tarjeta en un mes determinado.

**Datos:** tarjeta y período.

**Resultado:** detalle y total estimado de compras/cargos/cuotas aplicables al mes.

**También debe permitir:** conocer cantidad de cuotas restantes y mes de finalización de compras en cuotas.

---

## UC-15 Registrar cargo de tarjeta

**Objetivo:** registrar intereses, impuestos, comisiones u otros cargos variables.

**Datos:** tarjeta, fecha, importe, concepto y descripción opcional.

**Resultado:** nuevo gasto en el período correspondiente y aumento de deuda de tarjeta.

---

## UC-16 Pagar tarjeta

**Objetivo:** cancelar total o parcialmente deuda de tarjeta.

**Datos:** tarjeta, cuenta de pago, importe y fecha.

**Resultado:** disminuye la cuenta de activo y disminuye deuda de tarjeta.

**Reglas:** no genera gasto.

---

## UC-17 Registrar devolución/reintegro

**Objetivo:** registrar una devolución y la acreditación efectiva del dinero.

**Datos:** operación original opcional, importe, fecha, cuenta donde se acredita y descripción/motivo.

**Resultado:** el dinero se acredita en la cuenta indicada y la devolución se refleja correctamente en consultas de gasto.

**Reglas:** la cuenta receptora puede ser distinta de la cuenta usada originalmente; no se clasifica como ingreso ordinario.

---

## UC-18 Consultar saldos

**Objetivo:** conocer saldo de cuentas y deuda actual de tarjetas.

**Resultado:** saldos reconstruidos desde cargas iniciales y movimientos.

---

## UC-19 Consultar obligaciones

**Objetivo:** conocer compromisos pendientes, parciales, pagados y vencidos.

**Datos:** período/filtros opcionales.

**Resultado:** lista accionable de obligaciones y vencimientos.

---

## UC-20 Consultar gastos

**Objetivo:** consultar gastos por período y clasificación.

**Datos:** período y filtros opcionales.

**Resultado:** gastos agrupables por concepto/categoría.

---

## UC-21 Consultar ingresos

**Objetivo:** consultar ingresos reales por período y clasificación.

**Datos:** período y filtros opcionales.

**Resultado:** ingresos agrupables por concepto/categoría.

---

## UC-22 Importar extracto

**Objetivo:** incorporar movimientos externos para revisión.

**Datos:** archivo/fuente compatible y cuenta asociada cuando corresponda.

**Resultado:** movimientos importados pendientes, sin afectar saldos.

---

## UC-23 Sugerir clasificación de movimiento importado

**Objetivo:** reducir trabajo manual de conciliación.

**Resultado:** sugerencia de tipo de operación, concepto y relaciones aplicables usando reglas de conciliación.

**Reglas:** ninguna sugerencia puede crear un estado inválido.

---

## UC-24 Revisar movimiento importado

**Objetivo:** decidir qué hacer con una sugerencia.

**Opciones:** confirmar, corregir o ignorar.

**Resultado:** el movimiento sigue pendiente, queda ignorado o se convierte en una operación normal del dominio.

---

## UC-25 Crear regla de conciliación

**Objetivo:** reconocer automáticamente patrones recurrentes en extractos.

**Datos:** patrón, fuente/cuenta opcionales, clasificación sugerida y prioridad.

**Resultado:** futuras importaciones pueden recibir esa sugerencia.

---

## UC-26 Corregir operación

**Objetivo:** corregir una operación registrada erróneamente sin corromper saldos ni relaciones.

**Resultado:** el dominio recalcula o reemplaza sus efectos de forma trazable.

> La política exacta de edición vs anulación/reemplazo se definirá en una spec específica antes de implementarse.

---

## UC-27 Anular operación

**Objetivo:** revertir una operación que no debe seguir vigente.

**Resultado:** sus efectos financieros dejan de impactar el estado actual de forma trazable.

> El mecanismo exacto de anulación se definirá antes de su implementación.