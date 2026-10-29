# Sistema de Gestión Integral de Taller Mecánico

## Miembros del grupo L8-G07

1. Macías Verdugo, Jose Javier
2. García Bermúdez, Raúl
3. Romero de la Osa Díaz, Adrián


## 1. Introducción al problema

**TREVOR'S MOTOR** es una empresa dedicada a la reparación mecánica, eléctrica y de mantenimiento preventivo multimarca para turismos y vehículos comerciales ligeros, con más de 20 años de trayectoria en el sector.

La plantilla actual está compuesta por un Jefe de Taller, cuatro Oficiales Mecánicos/Electricistas, un Responsable de Almacén y dos Asesores de Servicio (Recepción y Facturación). El volumen medio de trabajo oscila entre 18 y 25 vehículos diarios.

A pesar de su solvencia técnica y alta demanda, la gestión operativa diaria depende de procesos analógicos y herramientas ofimáticas aisladas sin conexión entre sí, provocando cuellos de botella:
- **Órdenes de trabajo manuscritas:** La recepción y seguimiento se gestionan mediante fichas en soporte físico que viajan de mano en mano entre boxes y mostradores. Estas fichas sufren extravíos, deterioro por manchas y anotaciones incompletas o ilegibles de tiempos y piezas consumidas.
- **Falta de trazabilidad histórica:** No se dispone de un repositorio centralizado de vehículos y averías previas. Cuando un cliente reingresa por un fallo reiterado, los mecánicos no tienen acceso inmediato a las intervenciones pasadas, componentes sustituidos ni diagnósticos previos.
- **Descontrol en el inventario de repuestos:** La retirada de componentes del almacén no se descuenta en tiempo real. Esto produce desajustes de stock, retrasos al comenzar reparaciones por falta imprevista de material básico y duplicidad de pedidos a distribuidores.
- **Opacidad en presupuestos y cobros:** La comunicación de presupuestos suele realizarse telefónicamente o en notas informales. Los desacuerdos sobre costes sobrevenidos o partidas imprevistas al momento de facturar generan incidencias recurrentes y retrasos en los pagos.

El objetivo de este sistema es dotar a la empresa de una plataforma de software integral, que permita:
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

#### R.G.01. Gestionar la información de clientes y vehículos
Como **Asesor de Servicio**, quiero **consultar los datos e historiales de clientes y vehículos**, para **atenderlos y planificar sus revisiones**.

#### R.G.02. Planificar y controlar el trabajo del taller
Como **Jefe de Taller**, quiero **supervisar las órdenes, los mecánicos y los boxes**, para **organizar las reparaciones**.

#### R.G.03. Controlar el inventario de repuestos
Como **Responsable de Almacén**, quiero **conocer las existencias y los repuestos que faltan**, para **planificar la reposición**.

#### R.G.04. Gestionar presupuestos, facturación y cobros
Como **Asesor de Servicio**, quiero **consultar presupuestos, facturas y pagos pendientes**, para **controlar la gestión económica**.

### 3.2. Usuarios del sistema
- **Asesor de Servicio / Recepcionista:** Da de alta clientes y vehículos, genera órdenes de recepción, elabora presupuestos, expide facturas y gestiona los cobros.
- **Jefe de Taller:** Supervisa las operaciones, asigna vehículos y tareas a los operarios, valida diagnósticos complejos y firma el control de calidad final.
- **Mecánico / Oficial de Taller:** Consulta las órdenes de trabajo asignadas, registra los diagnósticos técnicos, imputa tiempos de mano de obra y da salida a los repuestos consumidos.
- **Responsable de Almacén:** Administra el catálogo de piezas, actualiza las existencias, gestiona compras a distribuidores y supervisa las alertas de stock crítico.
- **Administrador del Sistema:** Administra altas y bajas de usuarios, privilegios de acceso, auditorías y copias de seguridad de la base de datos.

La información del taller se almacenará de forma centralizada en **MariaDB**. Las órdenes podrán pasar por los estados **Abierta**, **Presupuestada**, **En Reparación**, **Esperando Repuesto**, **En Pruebas/Control de Calidad**, **Finalizada** y **Cerrada y Facturada**. El taller dispone de **cinco boxes** y su horario de trabajo se tendrá en cuenta para calcular la ocupación.

## 4. Catálogo de requisitos

### 4.1. Requisitos funcionales

#### R.F.01. Historial de reparaciones por vehículo
Como **Asesor de Servicio**, quiero **consultar por matrícula o VIN las órdenes de trabajo cerradas de un vehículo, con sus intervenciones, repuestos y facturas, y conocer el número total de reparaciones, las horas acumuladas de mano de obra y el importe total facturado, agrupados por vehículo y por año**, para **conocer sus antecedentes antes de una nueva recepción**.

#### R.F.02. Órdenes activas y carga de trabajo por mecánico
Como **Jefe de Taller**, quiero **consultar las órdenes no cerradas filtradas por estado y prioridad y obtener, para cada mecánico, el número de órdenes asignadas y la suma de las horas registradas en sus intervenciones**, para **comparar las cargas de trabajo y organizar los boxes disponibles**.

#### R.F.03. Repuestos con stock crítico agrupados por familia
Como **Responsable de Almacén**, quiero **consultar los repuestos cuyo stock disponible sea menor o igual que su mínimo, teniendo en cuenta las unidades reservadas en órdenes activas, y conocer por familia el número de referencias afectadas y la suma de unidades necesarias para recuperar el stock mínimo**, para **priorizar los pedidos de reposición a los proveedores**.

#### R.F.04. Presupuestos pendientes por cliente
Como **Asesor de Servicio**, quiero **consultar los presupuestos pendientes o caducados con sus clientes y vehículos, conocer los días transcurridos desde su emisión y obtener por cliente el número de presupuestos sin aprobar y la suma de sus importes**, para **priorizar los contactos de seguimiento**.

#### R.F.05. Facturación y saldos pendientes por período
Como **Asesor de Servicio**, quiero **consultar las facturas emitidas en un período y agruparlas por mes y cliente para conocer el número de facturas, la suma de importes facturados, la media por factura y la suma de saldos pendientes, distinguiendo los estados de cobro**, para **analizar la facturación y gestionar los pagos pendientes**.

#### R.F.06. Búsqueda de clientes y actividad de sus vehículos
Como **Asesor de Servicio**, quiero **buscar clientes por NIF/CIF, apellidos, teléfono o matrícula y obtener, para cada cliente, sus vehículos asociados, el número de vehículos, el número de órdenes cerradas y la fecha de la reparación más reciente**, para **localizar las fichas y conocer su actividad en el taller**.

#### R.F.07. Reparaciones recurrentes para revisión de garantía
Como **Jefe de Taller**, quiero **consultar las reparaciones facturadas de un vehículo y localizar las nuevas intervenciones sobre el mismo componente realizadas dentro de los 90 días posteriores o con una diferencia de kilometraje de hasta 2.000 km, contando las repeticiones por vehículo y componente**, para **identificar incidencias que deban revisarse como posibles casos de garantía**. Estos límites son **criterios de búsqueda del taller**, no una decisión automática sobre la garantía legal aplicable.

#### R.F.08. Previsiones de ITV y mantenimiento
Como **Asesor de Servicio**, quiero **consultar los vehículos cuya próxima ITV o revisión registrada venza en los siguientes 30 días, junto con los datos de contacto de sus titulares, y conocer el número de vencimientos agrupados por semana y tipo de mantenimiento**, para **organizar avisos y concertar citas**.

#### R.F.09. Costes y márgenes de las órdenes de trabajo
Como **Jefe de Taller**, quiero **consultar una orden de trabajo y calcular la suma de horas e importes de mano de obra por mecánico, la suma de costes y precios de venta de los repuestos por familia, el coste total estimado de la reparación, el importe facturable y su margen calculado sin IVA**, para **comparar el coste de los recursos utilizados con los ingresos de la intervención**.

#### R.F.10. Ocupación diaria de los boxes
Como **Asesor de Servicio**, quiero **consultar para una fecha las franjas de ocupación y los vehículos asignados a los cinco boxes, así como sumar las horas ocupadas por box y calcular su porcentaje de ocupación respecto al horario de trabajo**, para **identificar huecos disponibles y evitar la sobrecarga de las instalaciones**.

#### 4.1.1. Requisitos de información

##### R.I.01. Cliente
Como **Asesor de Servicio**, quiero **disponer de los datos de cada cliente**: NIF/CIF, nombre o razón social, primer apellido, segundo apellido, domicilio completo (calle, número, código postal, localidad y provincia), teléfono, correo electrónico y consentimiento para comunicaciones, para **identificarlo y contactar con él al gestionar reparaciones y facturas**.

##### R.I.02. Vehículo
Como **Asesor de Servicio**, quiero **disponer de los datos de cada vehículo**: matrícula, bastidor (VIN), marca, modelo, cilindrada, tipo de combustible, año de matriculación, kilometraje actual, cliente titular asociado, fecha prevista de próxima ITV, fecha prevista de próxima revisión y kilometraje objetivo del siguiente mantenimiento, para **identificarlo, consultar su historial y anticipar revisiones**.

##### R.I.03. Orden de trabajo
Como **Jefe de Taller**, quiero **disponer de la información de cada orden de trabajo**: código, matrícula del vehículo, fecha y hora de entrada, kilometraje de entrada y de salida, nivel de combustible, daños exteriores, motivo de visita, diagnóstico, prioridad, estado, fecha prevista de entrega, fecha de cierre, asesor responsable, mecánicos asignados, box y franjas de ocupación, autorización de reparación directa con su límite económico cuando proceda y validación del control de calidad; **de sus intervenciones** (identificador, mecánico, componente intervenido, descripción del trabajo, fecha, tiempo dedicado en fracciones de 0,25 horas, tarifa horaria aplicada y coste horario interno); y **de los repuestos reservados o consumidos** (referencia, unidades, precio de adquisición unitario aplicado y precio de venta unitario aplicado), para **seguir las reparaciones y calcular sus tiempos, costes y márgenes**.

##### R.I.04. Repuesto
Como **Responsable de Almacén**, quiero **disponer de los datos de cada repuesto**: referencia de fabricante, descripción, familia técnica, fabricante, proveedor habitual, stock físico, unidades reservadas en órdenes activas, stock mínimo, precio de adquisición unitario, precio de venta unitario y ubicación física en el almacén, para **controlar las existencias y calcular el stock disponible y las necesidades de reposición**.

##### R.I.05. Factura
Como **Asesor de Servicio**, quiero **disponer de los datos de cada factura**: serie y número correlativo, fecha de emisión, orden de trabajo asociada, datos fiscales del emisor y del cliente en el momento de emisión, conceptos facturados, base imponible, tipo y cuota de IVA, total, importe cobrado, saldo pendiente, estado de pago (pendiente, parcial o cobrada) y fechas, importes y métodos de los cobros asociados, para **controlar la facturación y los pagos pendientes**.

##### R.I.06. Presupuesto
Como **Asesor de Servicio**, quiero **disponer de los datos de cada presupuesto**: número, orden de trabajo asociada, fecha de emisión, tipo (inicial o ampliación), estado (pendiente, aceptado, rechazado o caducado), piezas y horas de mano de obra previstas con sus importes, base imponible, cuota de IVA, importe total, fecha límite de aceptación y fecha y constancia de la respuesta del cliente, para **hacer seguimiento de las propuestas y justificar la autorización de los trabajos**.

#### 4.1.2. Reglas de negocio

Cada regla expresa una restricción concreta y va acompañada de situaciones con las que se puede comprobar su cumplimiento.

##### R.N.01. NIF/CIF de cliente único
No pueden registrarse dos clientes con el mismo NIF/CIF.

**Pruebas de aceptación:**
- Dado un NIF/CIF no registrado, se permite crear el cliente.
- Si ya existe un cliente con ese NIF/CIF, se rechaza el alta duplicada.

##### R.N.02. Matrícula de vehículo única
No pueden registrarse dos vehículos con la misma matrícula.

**Pruebas de aceptación:**
- Se registra un vehículo con una matrícula no utilizada.
- Se rechaza otro vehículo con esa misma matrícula.

##### R.N.03. VIN de vehículo único
No pueden registrarse dos vehículos con el mismo VIN.

**Pruebas de aceptación:**
- Se admite un VIN válido que no figure en el sistema.
- Se rechaza un segundo vehículo con ese VIN.

##### R.N.04. Formato válido del VIN
El VIN debe tener exactamente 17 caracteres alfanuméricos permitidos, en mayúsculas y sin las letras I, O ni Q.

**Pruebas de aceptación:**
- Se admite un VIN de 17 caracteres válidos.
- Se rechaza un VIN de 16 caracteres o que contenga alguna de las letras excluidas.

##### R.N.05. Autorización previa de la reparación
Una orden de trabajo no puede pasar a **En Reparación** sin un presupuesto aceptado o, alternativamente, una autorización expresa de reparación directa registrada con un límite de importe.

**Pruebas de aceptación:**
- Se permite comenzar la reparación con un presupuesto aceptado o con una autorización directa válida dentro de su límite.
- Se impide comenzar la reparación si no existe ninguna de esas autorizaciones.

##### R.N.06. Aprobación de trabajos adicionales
No se pueden ejecutar trabajos que amplíen el presupuesto inicial sin la aceptación expresa de la ampliación por el cliente.

**Pruebas de aceptación:**
- Se permite registrar el trabajo adicional cuando su ampliación está aceptada.
- Se bloquea ese trabajo adicional si la ampliación continúa pendiente o ha sido rechazada.

##### R.N.07. Reservas de repuestos limitadas al stock disponible
No se pueden reservar para una orden más unidades de un repuesto que las disponibles. Cada reserva reduce el stock disponible sin descontar dos veces las mismas unidades del stock físico.

**Pruebas de aceptación:**
- Con 10 unidades físicas y 4 reservadas, se permite reservar 6 unidades más.
- Con esas mismas existencias, se rechaza una reserva de 7 unidades.

##### R.N.08. Espera por falta de repuestos
Una orden que requiera un repuesto sin stock suficiente debe permanecer en **Esperando Repuesto** mientras no se disponga de las unidades necesarias.

**Pruebas de aceptación:**
- Si faltan unidades requeridas, la orden pasa a **Esperando Repuesto**.
- Cuando se reciben suficientes unidades y se cumplen las restantes autorizaciones, la orden puede continuar su reparación.

##### R.N.09. Intervención necesaria para finalizar la orden
Una orden no puede pasar a **Finalizada** si no tiene registrada al menos una intervención con tiempo de mano de obra.

**Pruebas de aceptación:**
- Se impide finalizar una orden sin intervenciones registradas.
- Una orden con una intervención registrada supera esta comprobación.

##### R.N.10. Validación del control de calidad
Una orden no puede pasar a **Finalizada** sin el visto bueno registrado del Jefe de Taller.

**Pruebas de aceptación:**
- Se impide finalizar una orden sin validación del Jefe de Taller.
- Se permite superar esta comprobación cuando la validación está registrada.

##### R.N.11. Factura obligatoria para cerrar la orden
Una orden no puede pasar a **Cerrada y Facturada** sin tener una factura emitida asociada.

**Pruebas de aceptación:**
- Se rechaza el cierre de una orden que no tiene factura emitida.
- Una orden con factura emitida supera esta comprobación.

##### R.N.12. Kilometraje de salida obligatorio
Una orden no puede pasar a **Cerrada y Facturada** si no tiene registrado el kilometraje de salida del vehículo.

**Pruebas de aceptación:**
- Se impide el cierre si falta el kilometraje de salida.
- Con el kilometraje de salida registrado, se supera esta comprobación.

##### R.N.13. IVA general aplicado a los conceptos facturables
En el alcance previsto del taller, los conceptos sujetos al tipo general se calculan con **21 % de IVA**, mostrando por separado base imponible, cuota y total.

**Pruebas de aceptación:**
- Para una base imponible de 100 €, se calcula una cuota de 21 € y un total de 121 €.
- Se rechaza un presupuesto o factura cuyo total no coincida con la base y el IVA calculado.

##### R.N.14. Inmutabilidad de las facturas emitidas
Los datos fiscales y los conceptos de una factura emitida no pueden modificarse ni eliminarse físicamente. Los pagos posteriores se registran de forma asociada sin alterar su contenido fiscal.

**Pruebas de aceptación:**
- Se permite registrar un nuevo cobro asociado a una factura sin cambiar sus datos fiscales.
- Se rechaza un intento de cambiar sus conceptos facturados o borrar su registro.

##### R.N.15. Ausencia de solapamientos en un mismo box
Un mismo box no puede estar asignado a dos órdenes en franjas horarias solapadas.

**Pruebas de aceptación:**
- Se permite asignar dos órdenes al mismo box en franjas que no se solapan.
- Se rechaza la segunda asignación si coincide con una franja ocupada de ese box.

##### R.N.16. Caducidad de presupuestos pendientes
Un presupuesto que siga pendiente de aceptación después de **15 días naturales** desde su emisión pasa a **Caducado**.

**Pruebas de aceptación:**
- Un presupuesto pendiente con 15 días de antigüedad todavía no se marca como caducado.
- Un presupuesto pendiente con 16 días de antigüedad se marca como caducado.

### 4.2. Mapa de historias de usuario

Los **requisitos generales (RG)** son los objetivos de la fila superior. Debajo se agrupan los **requisitos funcionales (RF)** que desarrollan cada uno. Se mantienen los diez RF, cada uno bajo su objetivo principal.

| **R.G.01. Gestionar la información de clientes y vehículos** | **R.G.02. Planificar y controlar el trabajo del taller** | **R.G.03. Controlar el inventario de repuestos** | **R.G.04. Gestionar presupuestos, facturación y cobros** |
| :--- | :--- | :--- | :--- |
| **R.F.01.** Historial de reparaciones por vehículo | **R.F.02.** Órdenes activas y carga por mecánico | **R.F.03.** Repuestos con stock crítico | **R.F.04.** Presupuestos pendientes por cliente |
| **R.F.06.** Búsqueda de clientes y actividad de sus vehículos | **R.F.09.** Costes y márgenes de las órdenes | | **R.F.05.** Facturación y saldos pendientes |
| **R.F.07.** Reparaciones recurrentes para revisión de garantía | **R.F.10.** Ocupación diaria de boxes | | |
| **R.F.08.** Previsiones de ITV y mantenimiento | | | |

### 4.3. Requisitos no funcionales

#### R.N.F.01. Rendimiento de las consultas
Como **Asesor de Servicio y Jefe de Taller**, quiero **que las consultas habituales de históricos, stock y órdenes activas respondan en menos de 1,5 segundos en condiciones de concurrencia habitual**, para **evitar esperas en la atención y la gestión diaria del taller**.

#### R.N.F.02. Usabilidad en los boxes
Como **Mecánico de Taller**, quiero **utilizar una interfaz adaptable a tabletas, con textos legibles y controles táctiles amplios**, para **consultar y registrar intervenciones de forma cómoda en el entorno del taller**.

#### R.N.F.03. Seguridad y control de acceso
Como **Administrador del Sistema**, quiero **que los empleados accedan mediante cuentas individuales y que los datos y operaciones disponibles dependan de su perfil de usuario**, para **proteger la información personal, fiscal y operativa del taller frente a accesos no autorizados**.

#### R.N.F.04. Copias de seguridad y recuperación
Como **Administrador del Sistema**, quiero **que se realice una copia de seguridad automática de la base de datos al menos una vez al día y que exista un procedimiento de restauración**, para **poder recuperar la información tras un fallo o una pérdida de datos**.
