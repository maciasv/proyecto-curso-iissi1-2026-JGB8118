# Sistema de Gestión Integral de Taller Mecánico

## Miembros del grupo L8-G07

1. Macías Verdugo, Jose Javier
2. García Bermúdez, Raúl
3. Romero de la Osa Díaz, Adrián


## 1. Introducción al problema

**TREVOR MOTORS** es una empresa dedicada a la reparación mecánica, eléctrica y de mantenimiento preventivo multimarca para turismos y vehículos comerciales ligeros, con más de 20 años de trayectoria en el sector.

La plantilla actual está compuesta por un Jefe de Taller, cuatro Oficiales Mecánicos/Electricistas, un Responsable de Almacén y dos Asesores de Servicio (Recepción y Facturación). El volumen medio de trabajo oscila entre 18 y 25 vehículos diarios.

A pesar de su solvencia técnica y alta demanda, la gestión operativa diaria depende de procesos analógicos y herramientas ofimáticas aisladas sin conexión entre sí, provocando cuellos de botella:
- **Órdenes de trabajo manuscritas:** La recepción y seguimiento se gestionan mediante fichas en soporte físico que viajan de mano en mano entre boxes y mostradores. Estas fichas sufren extravíos, deterioro por manchas y anotaciones incompletas o ilegibles de tiempos y piezas consumidas.
- **Falta de trazabilidad histórica:** No se dispone de un repositorio centralizado de vehículos y averías previas. Cuando un cliente reingresa por un fallo reiterado, los mecánicos no tienen acceso inmediato a las intervenciones pasadas, componentes sustituidos ni diagnósticos previos.
- **Descontrol en el inventario de repuestos:** La retirada de componentes del almacén no se descuenta en tiempo real. Esto produce desajustes de stock, retrasos al comenzar reparaciones por falta imprevista de material básico y duplicidad de pedidos a distribuidores.
- **Opacidad en presupuestos y cobros:** La comunicación de presupuestos suele realizarse telefónicamente o en notas informales. Los desacuerdos sobre costes sobrevenidos o partidas imprevistas al momento de facturar generan incidencias recurrentes y retrasos en los pagos.

El objetivo de **TREVOR MOTORS** es dotar a la empresa de una plataforma de software integral, que permita:
1. Digitalizar el ciclo de vida de la Orden de Trabajo (OT) desde la recepción del vehículo hasta su entrega y facturación.
2. Centralizar el registro de clientes y vehículos con trazabilidad histórica completa.
3. Gestionar el catálogo y stock de recambios en tiempo real, controlando entradas, salidas e imputaciones por orden.
4. Mejorar la productividad monitorizando la carga horaria de los mecánicos y la ocupación de los boxes de trabajo.


<img width="922" height="515" alt="image" src="https://github.com/user-attachments/assets/24151b08-e6c0-4350-ba37-6dea51005ff4" />


## 2. Glosario de términos

- **Albarán de Recepción / Depósito:** Documento acreditativo entregado al titular del vehículo al depositar el automóvil en el taller, donde se hace constar el motivo de entrada, kilometraje actual, estado visual de entrega y autorización de custodia.
- **Bastidor (Número VIN):** *Vehicle Identification Number*. Código alfanumérico único de 17 caracteres normalizado internacionalmente (ISO 3779) que identifica de forma inequívoca un vehículo a motor.
- **Box de Trabajo:** Espacio físico delimitado del taller dotado de elevador hidráulico y utillaje necesario para llevar a cabo una intervención técnica simultánea.
- **Diagnóstico:** Inspección técnica y electrónica realizada por un operario para determinar el origen de una avería y dictaminar las operaciones y piezas requeridas.
- **Ficha Técnica (Tarjeta ITV):** Documentación oficial del vehículo que acredita sus características técnicas de homologación (código de motor, cilindrada, combustible, potencia y fecha de matriculación).
- **Mano de Obra:** Tiempo facturable dedicado por los técnicos a las operaciones de reparación y montaje, tasado en fracciones horarias o baremos oficiales del fabricante.
- **Presupuesto:** Documento económico previo y vinculante emitido por el taller donde se desglosan los costes previstos (recambios, mano de obra e impuestos) que requiere la aceptación expresa del cliente antes de iniciar la intervención.
- **Repuesto:** Componente físico o consumible (filtros, pastillas de freno, kits de distribución, fluidos) incorporado al vehículo durante su reparación.
- **Stock de seguridad:** Cantidad límite fijada para un repuesto en el almacén por debajo de la cual el sistema debe alertar para iniciar el proceso de reposición.

## 3. Visión general del sistema

### 3.1. Requisitos generales
1. **Centralización y persistencia de datos:** Registro consistente y normalizado de clientes, vehículos, órdenes de trabajo, piezas, intervenciones y facturas sobre MariaDB.
2. **Control de estados de reparación:** Gestión de los estados de la Orden de Trabajo (*Abierta*, *Presupuestada*, *En Reparación*, *Esperando Repuesto*, *En Pruebas/Control de Calidad*, *Finalizada*, *Cerrada y Facturada*).
3. **Gestión de inventario de repuestos:** Consulta inmediata de existencias, precio de coste y precio de venta al público (PVP).
4. **Seguridad y auditoría:** Control de accesos basado en perfiles de usuario y protección de datos según normativa vigente.

### 3.2. Usuarios del sistema
- **Asesor de Servicio / Recepcionista:** Da de alta clientes y vehículos, genera órdenes de recepción, elabora presupuestos, expide facturas y gestiona los cobros.
- **Jefe de Taller:** Supervisa las operaciones, asigna vehículos y tareas a los operarios, valida diagnósticos complejos y firma el control de calidad final.
- **Mecánico / Oficial de Taller:** Consulta las órdenes de trabajo asignadas, registra los diagnósticos técnicos, imputa tiempos de mano de obra y da salida a los repuestos consumidos.
- **Responsable de Almacén:** Administra el catálogo de piezas, actualiza las existencias, gestiona compras a distribuidores y supervisa las alertas de stock crítico.
- **Administrador del Sistema:** Administra altas y bajas de usuarios, privilegios de acceso, auditorías y copias de seguridad de la base de datos.

## 4. Catálogo de requisitos

### 4.1. Requisitos funcionales

#### R.F.01. Consulta de Historial Completo de Reparaciones por Vehículo
Como **Asesor de Servicio**  quiero **consultar el historial de intervenciones, kilometrajes y averías pasadas de un vehículo introduciendo su matrícula o bastidor (VIN)**  para **conocer los antecedentes mecánicos del vehículo antes de emitir una nueva recepción o presupuesto.**

**Prueba de aceptación:**
- El sistema solicita una matrícula o un VIN válido.
- Muestra en orden cronológico descendente las órdenes de trabajo cerradas del vehículo.
- Cada orden lista: fecha de entrada/salida, kilometraje registrado, avería declarada, piezas sustituidas y mecánicos intervinientes.
- Si el vehículo no registra reparaciones previas, informa que se trata de su primera visita.

#### R.F.02. Listado de Órdenes de Trabajo Activas según Estado y Prioridad
Como **Jefe de Taller**  quiero **listar las órdenes de trabajo activas filtradas por su estado actual y ordenadas por nivel de prioridad**  para **planificar la carga de trabajo de los mecánicos y asignar los boxes disponibles.**

**Prueba de aceptación:**
- Permite seleccionar uno o varios estados (*Presupuestada*, *En Reparación*, *Esperando Repuesto*, *Finalizada*).
- La tabla de resultados muestra: ID de orden, matrícula, marca/modelo, mecánico asignado, fecha de inicio y prioridad.
- Las órdenes con prioridad "Urgente" se muestran destacadas al inicio de la lista.

#### R.F.03. Consulta de Repuestos en Nivel Crítico (Bajo Stock Mínimo)
Como **Responsable de Almacén**  quiero **listar los repuestos cuya cantidad en stock sea inferior o igual a su stock mínimo definido**  para **tramitar a tiempo los pedidos de reaprovisionamiento a los proveedores.**

**Prueba de aceptación:**
- El sistema filtra automáticamente el catálogo donde `Stock_Actual <= Stock_Minimo`.
- Desglosa: código de referencia, descripción, fabricante, stock disponible, stock mínimo y proveedor habitual.
- Considera las piezas bloqueadas para reparaciones en curso según la regla **R.N.03**.

#### R.F.04. Listado de Presupuestos Pendientes de Aprobación
Como **Asesor de Servicio**  quiero **consultar los presupuestos emitidos que están pendientes de respuesta por parte del cliente**  para **realizar el seguimiento correspondiente y agilizar la aprobación de los trabajos.**

**Prueba de aceptación:**
- Muestra todos los presupuestos en estado "Pendiente de Aprobación".
- Presenta: número de presupuesto, fecha de emisión, cliente, teléfono de contacto, matrícula, importe total (con IVA) y días transcurridos.
- Marca como "Caducado" cualquier presupuesto emitido hace más de 15 días naturales.

#### R.F.05. Listado de Facturas Emitidas y Saldos Pendientes por Período
Como **Asesor de Servicio**  quiero **listar las facturas emitidas en un rango de fechas discriminando entre cobradas y pendientes de pago**  para **llevar el control de tesorería y gestionar cobros pendientes.**

**Prueba de aceptación:**
- Filtra por intervalo de fechas y estado de cobro (*Cobrada*, *Pendiente*, *Cobro Parcial*).
- Muestra: serie y número de factura, fecha, cliente, NIF/CIF, base imponible, importe de IVA, total y método de pago.
- Muestra totales acumulados de facturación y el importe pendiente de cobro.

#### R.F.06. Búsqueda Avanzada de Clientes y Vehículos Vinculados
Como **Asesor de Servicio**  quiero **buscar clientes por NIF/CIF, apellidos, teléfono o matrícula asociada**  para **localizar rápidamente su ficha de contacto y ver los vehículos registrados a su nombre.**

**Prueba de aceptación:**
- Permite búsquedas parciales o completas por cualquiera de los parámetros indicados.
- Lista los clientes coincidentes con sus datos de contacto y la relación de matrículas vinculadas.
- Al seleccionar un cliente, muestra los detalles de cada vehículo asociado (marca, modelo, año, VIN).

#### R.F.07. Consulta de Reparaciones en Garantía Activas
Como **Jefe de Taller**  quiero **consultar las reparaciones finalizadas en los últimos 3 meses o con menos de 2.000 km recorridos para un vehículo determinado**  para **dictaminar si una avería recurrente está amparada por la garantía legal sin coste para el cliente.**

**Prueba de aceptación:**
- Solicita matrícula y descripción/componente de la avería.
- Localiza facturas cerradas con antigüedad menor a 90 días naturales o una diferencia de odómetro menor a 2.000 km.

#### R.F.08. Listado de Previsiones de Mantenimiento e Inspección ITV
Como **Asesor de Servicio**  quiero **listar los vehículos registrados cuyas revisiones periódicas o fecha de ITV venzan en los próximos 30 días**  para **contactar preventivamente con los clientes y concertar citas de mantenimiento.**

**Prueba de aceptación:**
- Calcula vencimientos basados en la periodicidad de la ITV o en la proyección de kilometraje anual del cliente.
- Muestra: cliente, teléfono, matrícula, concepto de mantenimiento próximo e intervalo de fecha previsto.
- Permite exportar o tabular la información resultante.

#### R.F.09. Consulta Detallada de Costes y Desglose de una Orden de Trabajo
Como **Jefe de Taller**  quiero **consultar el desglose pormenorizado de costes de una Orden de Trabajo concreta**  para **verificar el sumatorio exacto de recambios, tiempos de mano de obra y márgenes brutos calculados.**

**Prueba de aceptación:**
- Solicita el código identificativo de la orden.
- Desglosa: líneas de repuestos utilizados (referencia, unidades, precio coste, PVP), líneas de mano de obra (mecánico, horas dedicadas, tarifa horaria aplicada) y subtotales.
- Valida el desglose de IVA conforme a la regla **R.N.05**.

#### R.F.10. Consulta de Ocupación Diaria de Boxes de Trabajo
Como **Asesor de Servicio**  quiero **consultar la ocupación horaria de cada uno de los 5 boxes del taller para una fecha determinada**  para **asignar citas de recepción sin sobrecargar la capacidad instalada del taller.**

**Prueba de aceptación:**
- Solicita una fecha de consulta (por defecto, la fecha en curso).
- Muestra una cuadrícula horaria con el estado de cada box: vehículo ubicado, tipo de reparación y franja horaria ocupada.
- Resalta los periodos libres disponibles para intervenciones rápidas.


#### 4.1.1. Requisitos de información

##### R.I.01. Gestión de Clientes y Titulares
Como **Asesor de Servicio**  quiero **almacenar los datos identificativos, fiscales y de contacto de los clientes particulares y empresas**  para **mantener un registro fidedigno para la emisión legal de presupuestos, facturas y avisos.**

**Prueba de aceptación:**
- Almacena de forma persistente: NIF/CIF (clave única), Nombre o Razón Social, Primer Apellido, Segundo Apellido, Domicilio completo (calle, número, código postal, localidad, provincia), Teléfono principal, Correo electrónico y Consentimiento explícito de comunicaciones RGPD (booleano).
- No admite duplicados de NIF/CIF.

##### R.I.02. Ficha Técnica de Vehículos
Como **Asesor de Servicio**  quiero **almacenar la información técnica e identificativa de cada vehículo registrado en el taller**  para **conocer sus características mecánicas y vincularlo unívocamente a su propietario titular.**

**Prueba de aceptación:**
- Almacena: Matrícula (clave única), Número de Bastidor/VIN (único, 17 caracteres), Marca, Modelo, Cilindrada, Tipo de Combustible (Gasolina, Diésel, Híbrido, Eléctrico, GLP), Año de Matriculación, Kilometraje actual registrado y NIF del Cliente propietario asociado.
- Cumple con la regla de negocio **R.N.01**.

##### R.I.03. Órdenes de Trabajo (OT)
Como **Jefe de Taller**  quiero **almacenar el ciclo de vida y los datos operativos de cada intervención en el taller**  para **disponer de una trazabilidad completa de cada reparación efectuada.**

**Prueba de aceptación:**
- Almacena: Código de Orden (secuencial único), Matrícula del vehículo, Fecha y hora de entrada, Kilometraje de entrada, Nivel de combustible, Daños visuales exteriores declarados, Motivo de visita/Avería declarada, Prioridad (*Normal*, *Urgente*), Estado actual, Fecha prevista de entrega, Fecha de cierre real y Asesor de servicio responsable.
- Respeta la máquina de estados definida en **R.N.04**.

##### R.I.04. Catálogo y Almacén de Repuestos
Como **Responsable de Almacén**  quiero **almacenar los datos técnicos, existencias y precios del catálogo de recambios y consumibles**  para **mantener el inventario controlado y valorar económicamente los materiales de cada orden.**

**Prueba de aceptación:**
- Almacena: Código de referencia de fabricante (único), Descripción de la pieza, Familia técnica (Motor, Frenado, Suspensión, Eléctrico, Fluidos), Fabricante, Stock actual disponible, Stock mínimo de seguridad, Precio de adquisición unitario (sin IVA), Precio de venta al público (PVP sin IVA) y Ubicación física (estantería/módulo).
- El stock actual no puede tomar valores inferiores a cero.

##### R.I.05. Registro de Intervenciones y Mano de Obra
Como **Jefe de Taller**  quiero **almacenar el registro de tiempos dedicados por cada técnico a cada orden de trabajo**  para **computar la mano de obra facturable y auditar los tiempos productivos de los técnicos.**

**Prueba de aceptación:**
- Almacena: Identificador de intervención, Código de la OT, DNI/Identificador del Mecánico, Descripción del trabajo realizado, Tiempo dedicado (en horas y fracciones decimales de 0.25 h), tarifa horaria aplicada y Fecha de ejecución.

##### R.I.06. Facturas y Comprobantes de Cobro
Como **Asesor de Servicio**  quiero **almacenar los documentos formales de facturación y transacciones de cobro emitidos**  para **cumplir con la normativa tributaria y respaldar la garantía mercantil de las reparaciones.**

**Prueba de aceptación:**
- Almacena: Serie y número correlativo inalterable de factura, Fecha de emisión, Código de la OT, Datos fiscales congelados de emisor y cliente, Líneas detalladas de facturación, Base imponible, Tipo impositivo de IVA, Importe total, Estado de pago (*Cobrada*, *Pendiente*) y Método de pago empleado.
- Una vez registrada, la factura no admite modificación ni borrado físico.

#### 4.1.2. Reglas de negocio

##### R.N.01. Unicidad e Integridad de Identificadores del Vehículo (Matrícula y VIN)
**Descripción:**  
No podrán coexistir en el sistema dos vehículos registrados con la misma matrícula o el mismo Número de Bastidor (VIN). El VIN debe cumplir estrictamente con el estándar internacional ISO 3779, estando compuesto por exactamente 17 caracteres alfanuméricos en mayúsculas, quedando excluidas las letras I, O y Q para prevenir confusiones de lectura. La matrícula debe seguir un formato oficial de matriculación legalmente homologado.

##### R.N.02. Aprobación Expresa Previa del Presupuesto
**Descripción:**  
Ninguna Orden de Trabajo podrá pasar al estado *"En Reparación"* si tiene un presupuesto asociado en estado diferente a *"Aceptado"*, a menos que en el albarán de recepción conste la renuncia expresa del cliente a la elaboración de presupuesto previo con autorización de reparación directa hasta un importe prefijado. Toda avería no contemplada inicialmente requerirá la emisión de un presupuesto anexo y su aprobación explícita antes de acometerla.

##### R.N.03. Control y Bloqueo de Stock de Repuestos por Orden Activa
**Descripción:**  
Cuando un mecánico asigna piezas a una Orden de Trabajo activa, las unidades de dicho repuesto deben deducirse de inmediato del stock disponible y asignarse a la orden. Si la cantidad disponible de una pieza en almacén es inferior a la requerida para la reparación, la orden pasará de forma automática al estado *"Esperando Repuesto"* y se bloqueará el avance del estado operativo hasta la entrada física del pedido a almacén.

##### R.N.04. Cierre y Facturación de Orden de Trabajo
**Descripción:**  
Una Orden de Trabajo no puede ser catalogada como *"Finalizada"* si no cuenta con al menos una línea de mano de obra imputada por un mecánico cualificado y la validación final del Jefe de Taller certificando el control de calidad. Asimismo, ninguna orden podrá pasar al estado *"Cerrada"* sin haber generado su factura legal correlativa y haber registrado el kilometraje de entrega formal del vehículo al titular.

##### R.N.05. Imputación de Tipos Impositivos (IVA General 21%)
**Descripción:**  
Todas las líneas de conceptos facturables (tanto piezas como horas de mano de obra) devengarán obligatoriamente el tipo impositivo del 21% de IVA (Impuesto sobre el Valor Añadido para régimen general). En todos los presupuestos y facturas emitidas deberán figurar desglosados de manera obligatoria: la Base Imponible, la Cuota Tributaria de IVA y el Importe Total Resultante.

### 4.2. Mapa de historias de usuario
| Actividad Principal | 1. Recepción y Entrada | 2. Diagnóstico y Presupuesto | 3. Intervención en Taller | 4. Control de Calidad | 5. Entrega y Facturación |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Paso de Usuario** | Identificar cliente y vehículo; inspección física inicial. | Detectar averías, evaluar piezas requeridas y cotizar. | Desmontaje, reparación y consumo de horas y piezas. | Verificación técnica y pruebas dinámicas en carretera. | Liquidación económica, cobro y entrega del vehículo. |
| **Historias / Tareas de Usuario** | - Buscar cliente por NIF o matrícula<br>- Dar de alta cliente y vehículo<br>- Abrir Orden de Trabajo | - Registrar diagnóstico técnico<br>- Consultar disponibilidad de repuestos<br>- Generar presupuesto<br>- Consultar presupuestos pendientes | - Consultar OTs asignadas por prioridad<br>- Imputar mano de obra<br>- Imputar piezas consumidas<br>- Consultar costes acumulados | - Realizar chequeo final en carretera<br>- Registrar visto bueno del Jefe de Taller<br>- Pasar orden a estado "Finalizada" | - Generar factura legal con IVA<br>- Verificar garantías vigentes<br>- Consultar cobros pendientes<br>- Registrar pago y cerrar OT |

### 4.3. Requisitos no funcionales

#### R.N.F.01. Rendimiento y Latencia de Consultas
Como **Asesor de Servicio y Jefe de Taller**  quiero **que el sistema devuelva las consultas y listados de información (históricos de vehículos, stock y órdenes activas) en un tiempo inferior a 1,5 segundos en condiciones de concurrencia habitual**  para **evitar esperas en el mostrador de atención al público y mantener la fluidez operativa.**

#### R.N.F.02. Usabilidad y Diseño Responsive para Entornos de Taller
Como **Mecánico de Taller**  quiero **que la interfaz del sistema esté optimizada para tablets táctiles ubicadas en los boxes, con áreas de pulsación amplias y alta legibilidad de contrastes**  para **facilitar la consulta y el registro de datos incluso llevando guantes de trabajo o bajo condiciones de luz cambiantes.**
