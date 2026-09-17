# perFin — Modelo de dominio V1

> Estado: borrador consolidado para revisión funcional. Sólo contiene decisiones acordadas hasta el momento.

## 1. Objetivo

El dominio debe permitir conocer con bajo esfuerzo:

- gastos del período;
- qué gastos fueron pagados y cuáles siguen pendientes de pago;
- obligaciones pendientes y vencidas;
- compromisos futuros por cuotas;
- deuda y pagos estimados por tarjeta y mes;
- origen de los ingresos;
- movimientos reales registrados.

## 2. Cuenta

Representa un lugar donde existe dinero o deuda.

Tipos iniciales:

- `BANCO`
- `EFECTIVO`
- `BILLETERA`
- `TARJETA_CREDITO`

Las primeras tres son cuentas de activo. Una tarjeta representa deuda.

Atributos conceptuales mínimos:

- Id
- Nombre
- Tipo
- Activa

La cuenta conserva los efectos necesarios para registrar correctamente operaciones y reconstruir su historial. La visualización de dinero disponible o saldo actual no constituye un objetivo funcional de V1.

## 3. Tarjeta

Representa la configuración específica de una cuenta `TARJETA_CREDITO`.

Atributos conceptuales:

- Id
- CuentaId
- DiaCierre
- CierreInclusivo
- DiaVencimiento
- CuentaPagoPredeterminadaId (opcional)

La asignación de una compra al período correspondiente se determina automáticamente por fecha de compra y cierre.

El usuario no selecciona manualmente el ciclo normal de una compra.

## 4. Concepto

Clasifica económicamente ingresos y egresos.

Atributos conceptuales:

- Id
- Nombre
- Tipo: `INGRESO` o `EGRESO`
- Categoria
- Subcategoria (opcional)
- Activo

Ejemplos de ingreso:

- Sueldo
- Desarrollo freelance
- Venta
- Otros ingresos

Ejemplos de egreso:

- Alquiler
- Electricidad
- Gas
- Supermercado
- Combustible
- Intereses
- Impuestos
- Comisiones

El concepto no contiene información de recurrencia, cuenta o medio de pago.

## 5. Movimiento

Representa un hecho efectivamente registrado en el sistema.

Atributos conceptuales:

- Id
- Fecha
- TipoOperacion
- ConceptoId cuando corresponda
- Importe
- Descripcion opcional
- MovimientoRelacionadoId opcional
- FechaCreacion

Tipos de operación identificados:

- `INGRESO`
- `EGRESO`
- `PAGO_OBLIGACION`
- `TRANSFERENCIA`
- `COMPRA_TARJETA`
- `PAGO_TARJETA`
- `CARGO_TARJETA`
- `DEVOLUCION_REINTEGRO`
- `CARGA_INICIAL`

Los importes se expresan como valores positivos; el efecto financiero se representa mediante impactos sobre cuentas.

## 6. ImpactoCuenta

Componente interno que representa el efecto de un movimiento sobre una cuenta.

Atributos conceptuales:

- Id
- MovimientoId
- CuentaId
- Variacion

El usuario nunca crea impactos manualmente.

Ejemplos:

### Transferencia banco → efectivo

- Banco: -100.000
- Efectivo: +100.000

### Pago de tarjeta

- Banco: -200.000
- Tarjeta: -200.000 de deuda

### Ingreso

- Banco: +1.500.000

## 7. Carga inicial

Permite establecer el punto de partida sin reconstruir historia anterior.

Se registra como una operación administrativa `CARGA_INICIAL`.

Ejemplos:

- Banco: saldo inicial +500.000
- Efectivo: saldo inicial +50.000
- Tarjeta: deuda inicial +300.000

No constituye ingreso ni gasto.

## 8. Regla recurrente

Representa un compromiso de egreso que se repite.

No se utiliza para ingresos.

Atributos conceptuales:

- Id
- ConceptoId
- Frecuencia
- FechaInicio
- FechaFin opcional
- TipoMonto
- MontoBase opcional
- DiaVencimiento
- Activa

Una regla genera obligaciones concretas. La regla no es una deuda por sí misma.

## 9. Obligación

Representa un gasto comprometido concreto que puede estar pendiente, parcialmente pagado, pagado o vencido.

Es la entidad que permite responder una necesidad central de V1: distinguir qué gastos ya fueron pagados y cuáles todavía deben pagarse.

Atributos conceptuales:

- Id
- ConceptoId
- ReglaRecurrenteId opcional
- Periodo
- MontoEstimado opcional
- MontoFinal opcional
- FechaVencimiento
- Estado derivado

Estados funcionales:

- `PENDIENTE`
- `PARCIAL`
- `PAGADA`
- `VENCIDA`

Puede provenir de una regla recurrente o ser creada manualmente.

Puede recibir pagos parciales o totales.

## 10. Pago de obligación

Se registra como movimiento `PAGO_OBLIGACION` asociado a una obligación.

- desde banco: disminuye banco;
- desde efectivo: disminuye efectivo;
- mediante tarjeta: satisface la obligación con el proveedor y genera deuda en la tarjeta según las reglas aplicables.

La deuda con el proveedor y la deuda con el emisor de la tarjeta son conceptos distintos.

## 11. Compra con tarjeta

Toda compra con tarjeta se imputa al mes determinado por el cierre de la tarjeta.

Ejemplo:

- compra: 28/09;
- cierre: día 25;
- entra en período de octubre;
- el gasto corresponde a octubre.

Pagar posteriormente el resumen no genera un nuevo gasto.

## 12. Compra en cuotas

Una compra en cuotas representa un compromiso de pago futuro distribuido en meses sucesivos.

El usuario necesita saber:

- importe estimado a pagar por tarjeta en cada mes;
- cantidad de cuotas;
- cuota actual estimada para un período;
- cuándo finaliza el compromiso.

Entidad conceptual:

### CompraEnCuotas

- Id
- ConceptoId
- TarjetaId
- FechaCompra
- CantidadCuotas
- ImporteCuota
- Descripcion opcional

No existe una entidad `Cuota` independiente en V1.

El calendario de cuotas se deriva de:

- fecha de compra;
- configuración de cierre de la tarjeta;
- cantidad de cuotas;
- importe de cuota.

Ejemplo:

Compra 28/09, cierre día 25, 3 cuotas de 100.000:

- octubre: 100.000
- noviembre: 100.000
- diciembre: 100.000

No se reconoce el valor total de la compra como gasto inicial.

## 13. Cargos de tarjeta

Intereses, impuestos, comisiones y cargos similares son nuevos gastos variables.

Se registran como `CARGO_TARJETA`, con concepto de egreso, y afectan el período de tarjeta correspondiente.

No son pagos de la compra original.

## 14. Pago de tarjeta

Se registra como `PAGO_TARJETA`.

Ejemplo:

- Banco: -300.000
- Tarjeta: -300.000 de deuda

No tiene concepto de gasto porque el gasto fue reconocido por las operaciones que originaron la deuda.

## 15. Devolución y reintegro

Es una operación explícita `DEVOLUCION_REINTEGRO`.

Puede relacionarse con el movimiento original.

El usuario registra manualmente dónde fue acreditado el dinero.

Ejemplo real permitido:

- compra con débito desde banco;
- devolución posterior;
- reintegro acreditado en una billetera virtual.

La cuenta receptora del reintegro no tiene que coincidir con la cuenta usada en la compra.

El reintegro no se clasifica como ingreso ordinario.

## 16. Ingresos

No existe `IngresoEsperado` ni planificación de ingresos en V1.

Un ingreso sólo existe cuando ocurre y se clasifica mediante un concepto.

Ejemplos:

- Sueldo
- Desarrollo freelance
- Venta
- Otros ingresos

## 17. Importación bancaria

Una importación es una capa previa al dominio financiero confirmado.

### Importacion

- Id
- Fuente
- FechaImportacion
- IdentificadorArchivo opcional

### MovimientoImportado

- Id
- ImportacionId
- Fecha
- DescripcionOriginal
- Importe
- Estado
- ClasificacionSugerida

Estados:

- `PENDIENTE`
- `CONFIRMADO`
- `IGNORADO`

Un movimiento importado pendiente no afecta saldos.

Al confirmarse, produce una operación normal del dominio.

## 18. Regla de conciliación

Sirve para sugerir la interpretación de movimientos importados.

Atributos conceptuales:

- Id
- Patron
- Fuente opcional
- CuentaId opcional
- TipoOperacionSugerido
- ConceptoId opcional
- Activa
- Prioridad

Una regla de conciliación sólo sugiere. Nunca puede violar invariantes del dominio.

## 19. Invariantes principales

- Una transferencia entre cuentas propias no genera ingreso ni gasto.
- Una transferencia no puede tener la misma cuenta como origen y destino.
- Un pago de tarjeta no genera gasto.
- Un pago de tarjeta debe afectar una tarjeta y una cuenta de activo.
- Un pago en banco o efectivo no puede tener ciclo de tarjeta.
- Una compra con tarjeta sólo utiliza una cuenta configurada como tarjeta.
- El período de una compra con tarjeta se determina automáticamente por el cierre.
- Una compra en cuotas no reconoce el valor total como gasto inicial.
- Las cuotas futuras se calculan; no se administran como entidades independientes en V1.
- Una obligación no puede tener saldo pendiente negativo.
- Una obligación totalmente cancelada está pagada.
- Una devolución debe indicar dónde fue acreditado realmente el reintegro.
- Un reintegro no es ingreso ordinario.
- Una carga inicial no constituye ingreso ni gasto.
- Un movimiento importado pendiente no afecta saldos.
- Los saldos deben poder reconstruirse desde cargas iniciales y movimientos.

## 20. Fuera de alcance V1

- ingresos esperados;
- presupuestos mensuales;
- objetivos de ahorro;
- inversiones;
- cotizaciones;
- patrimonio inmobiliario;
- multi-moneda;
- préstamos complejos;
- automatización bancaria en tiempo real;
- predicciones financieras.

## 21. Regla para Codex

Codex no puede introducir nuevas entidades de dominio, estados financieros o reglas económicas para resolver una conveniencia de implementación. Si una spec necesita algo no definido aquí, la decisión vuelve al proceso de especificación.