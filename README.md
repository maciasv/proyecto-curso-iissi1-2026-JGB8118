# Sistema de Gestión de Taller Mecánico

## Miembros del grupo L8-G07

1. Macías Verdugo, Jose Javier
2. García Bermúdez, Raúl
3. Romero de la Osa Díaz, Adrián

## 1. Introducción al problema

**TREVOR'S MOTOR** es un taller dedicado a la reparación y el mantenimiento de vehículos. La gestión mediante fichas y documentos separados dificulta consultar los trabajos anteriores, controlar los repuestos y conocer los importes de las reparaciones.

Este proyecto propone una aplicación de alcance reducido con un único perfil de usuario: **Mecánico**. Permite registrar clientes y vehículos, seguir las órdenes de trabajo, anotar intervenciones y repuestos, consultar presupuestos y controlar los importes facturados y sus saldos.

Los objetivos son centralizar los historiales, consultar la carga de trabajo, conocer las existencias y realizar consultas con cálculos y agrupaciones. El control de pagos se limita al **importe cobrado acumulado de cada factura**, sin historial de pagos individuales.

## 2. Glosario de términos

- **Orden de trabajo (OT):** registro que agrupa la reparación de un vehículo.
- **Intervención:** trabajo concreto realizado por un mecánico dentro de una orden.
- **Mano de obra:** horas de una intervención y su valor económico.
- **VIN o bastidor:** identificador del vehículo utilizado junto con la matrícula.
- **Repuesto:** pieza o material utilizado en una reparación.
- **Uso de repuesto:** cantidad, estado y precios de un repuesto en una orden concreta.
- **Stock disponible:** stock físico menos las unidades reservadas.
- **Presupuesto:** propuesta de importe de una reparación que puede aceptarse, rechazarse o caducar.
- **Factura:** registro del importe facturado por una orden y de su saldo.

## 3. Visión general del sistema

### 3.1. Requisitos generales

#### R.G.01. Gestionar la información de clientes y vehículos
Como **Mecánico**, quiero **consultar los datos e historiales de clientes y vehículos**, para **conocer sus antecedentes y revisiones**.

#### R.G.02. Planificar y controlar el trabajo del taller
Como **Mecánico**, quiero **consultar las órdenes, los trabajos y sus horas**, para **organizar las reparaciones**.

#### R.G.03. Controlar el inventario de repuestos
Como **Mecánico**, quiero **consultar existencias y reservas**, para **detectar necesidades de reposición**.

#### R.G.04. Gestionar presupuestos, facturación y cobros
Como **Mecánico**, quiero **consultar presupuestos, importes facturados y saldos**, para **llevar el control económico básico**.

### 3.2. Usuario del sistema

**Mecánico:** registra y consulta clientes, vehículos, órdenes, intervenciones, repuestos, presupuestos e importes facturados. También registra el control de calidad. Pueden existir varios mecánicos, todos con el mismo perfil.

La información se almacenará en **MariaDB**. Los estados de una orden son **Abierta**, **Presupuestada**, **En Reparación**, **En Pruebas/Control de Calidad**, **Finalizada** y **Cerrada y Facturada**. Los datos de cierre pueden no existir cuando se abre la orden.

## 4. Catálogo de requisitos

### 4.1. Requisitos funcionales

#### R.F.01. Historial de reparaciones por vehículo
Como **Mecánico**, quiero **consultar por matrícula o VIN las órdenes cerradas de un vehículo, sus intervenciones, repuestos y facturas, y obtener el número de reparaciones, las horas de mano de obra y el importe facturado, agrupados por vehículo y año**, para **conocer sus antecedentes**.

#### R.F.02. Órdenes activas y carga de trabajo por mecánico
Como **Mecánico**, quiero **consultar las órdenes no cerradas por estado y prioridad y obtener, para cada mecánico, el número de órdenes asignadas y la suma de horas registradas en sus intervenciones**, para **comparar las cargas de trabajo**.

#### R.F.03. Repuestos con stock crítico agrupados por familia
Como **Mecánico**, quiero **consultar los repuestos cuyo stock disponible sea menor o igual que su mínimo, teniendo en cuenta los usos en estado Reservado, y obtener por familia el número de referencias afectadas y la suma de unidades necesarias para recuperar el mínimo**, para **detectar necesidades de reposición**.

#### R.F.04. Presupuestos pendientes por cliente
Como **Mecánico**, quiero **consultar los presupuestos pendientes o caducados con sus clientes y vehículos, calcular los días desde su emisión y obtener por cliente el número de presupuestos sin aprobar y la suma de sus importes**, para **organizar el seguimiento**.

#### R.F.05. Facturación y saldos por período
Como **Mecánico**, quiero **agrupar las facturas de un período por mes y cliente para obtener su número, el importe total, la media por factura y la suma de saldos pendientes, distinguiendo el estado de pago**, para **controlar los importes facturados y por cobrar**.

#### R.F.06. Búsqueda de clientes y actividad de sus vehículos
Como **Mecánico**, quiero **buscar clientes por NIF/CIF, apellidos, teléfono o matrícula y consultar sus vehículos, el número de vehículos asociados, el número de órdenes cerradas y la fecha de la reparación más reciente**, para **localizar sus fichas y conocer su actividad**.

#### R.F.07. Reparaciones recurrentes
Como **Mecánico**, quiero **consultar las reparaciones facturadas de un vehículo y detectar intervenciones posteriores sobre el mismo componente dentro de los 90 días siguientes o con una diferencia de kilometraje de hasta 2.000 km, contando las repeticiones por vehículo y componente**, para **identificar averías que deban revisarse**.

Los límites de tiempo y kilometraje son criterios de búsqueda del proyecto, no una decisión automática sobre garantías.

#### R.F.08. Previsiones de ITV y revisión
Como **Mecánico**, quiero **consultar los vehículos cuya próxima ITV o revisión registrada venza en los siguientes 30 días, junto con los datos de contacto de sus titulares, y contar los vencimientos por semana y tipo (ITV o revisión)**, para **organizar los avisos a clientes**.

#### R.F.09. Costes y márgenes de las órdenes
Como **Mecánico**, quiero **consultar una orden y sumar las horas e importes de mano de obra por mecánico y los costes y precios de venta de los repuestos consumidos por familia, calculando el coste total, el importe facturable y el margen sin IVA**, para **comparar costes e ingresos**.

#### 4.1.1. Requisitos de información

##### R.I.01. Cliente
Como **Mecánico**, quiero **disponer de la información de cliente**: NIF/CIF, nombre, apellidos, teléfono y correo electrónico, para **identificar al cliente y contactar con él**.

##### R.I.02. Vehículo
Como **Mecánico**, quiero **disponer de la información de vehículo**: matrícula, VIN, marca, modelo, kilometraje actual, cliente titular, fecha de próxima ITV y fecha de próxima revisión (estas dos fechas pueden no conocerse todavía), para **identificarlo, consultar su historial y anticipar revisiones**.

##### R.I.03. Orden de trabajo
Como **Mecánico**, quiero **disponer de la información de orden de trabajo**: código, vehículo, fecha de entrada, fecha de cierre cuando exista, kilometraje de entrada y de salida cuando se conozca, estado, prioridad, autorización directa y su límite cuando proceda, y control de calidad validado; los mecánicos asignados (código y nombre); las intervenciones (código, descripción, componente, fecha, horas, tarifa y coste horario, y mecánico que las realiza); y los repuestos utilizados con sus unidades, estado (Reservado o Consumido) y precios de coste y venta aplicados, para **seguir las reparaciones y calcular sus tiempos e importes**.

##### R.I.04. Repuesto
Como **Mecánico**, quiero **disponer de la información de repuesto**: referencia, descripción, familia, stock físico, stock mínimo, precio de coste y precio de venta; las unidades reservadas y el stock disponible se calculan a partir de los usos registrados, para **controlar las existencias**.

##### R.I.05. Factura
Como **Mecánico**, quiero **disponer de la información de factura**: número, fecha de emisión, orden asociada, cliente destinatario, base imponible, cuota de IVA, total, importe cobrado acumulado, saldo pendiente y estado de pago (Pendiente, Parcial o Cobrada), para **controlar los importes de cada reparación**.

##### R.I.06. Presupuesto
Como **Mecánico**, quiero **disponer de la información de presupuesto**: número, orden asociada, fecha de emisión, estado (Pendiente, Aceptado, Rechazado o Caducado), base imponible, cuota de IVA e importe total, para **hacer seguimiento de la propuesta económica**.

#### 4.1.2. Reglas de negocio

Las pruebas de aceptación se incluyen únicamente en las reglas de negocio. La numeración de esta versión va de RN01 a RN12.

##### R.N.01. NIF/CIF de cliente único
No pueden registrarse dos clientes con el mismo NIF/CIF.

**Pruebas de aceptación:**
- Se admite un cliente con un NIF/CIF no registrado.
- Se rechaza otro cliente con ese mismo NIF/CIF.

##### R.N.02. Matrícula única
No pueden registrarse dos vehículos con la misma matrícula.

**Pruebas de aceptación:**
- Se admite un vehículo con una matrícula no utilizada.
- Se rechaza otro vehículo con esa misma matrícula.

##### R.N.03. VIN único
No pueden registrarse dos vehículos con el mismo VIN.

**Pruebas de aceptación:**
- Se admite un VIN válido que no figure en el sistema.
- Se rechaza un segundo vehículo con ese VIN.

##### R.N.04. Formato del VIN
El VIN tiene exactamente 17 caracteres alfanuméricos en mayúsculas, sin I, O ni Q.

**Pruebas de aceptación:**
- Se admite un VIN de 17 caracteres permitidos.
- Se rechaza un VIN de 16 caracteres o con una letra excluida.

##### R.N.05. Autorización previa
Una orden no puede pasar a En Reparación sin un presupuesto aceptado o una autorización directa expresa con límite de importe registrado y respetado.

**Pruebas de aceptación:**
- Se admite el inicio con presupuesto aceptado o autorización directa dentro de su límite.
- Se rechaza el inicio sin ninguna de esas autorizaciones.

##### R.N.06. Reservas limitadas al stock disponible
No se pueden reservar más unidades que las disponibles. Reservar no descuenta el stock físico; al consumir se descuenta una vez y se libera la reserva.

**Pruebas de aceptación:**
- Con 10 unidades físicas y 4 reservadas se admite reservar otras 6.
- Con esas mismas existencias se rechaza una reserva de 7 unidades.

##### R.N.07. Intervención necesaria para finalizar
Una orden no puede pasar a Finalizada sin al menos una intervención con horas de trabajo registradas.

**Pruebas de aceptación:**
- Una orden con una intervención de 2 horas supera esta comprobación.
- Se impide finalizar una orden sin intervenciones.

##### R.N.08. Control de calidad validado
Una orden no puede pasar a Finalizada sin que el mecánico haya registrado la validación del control de calidad.

**Pruebas de aceptación:**
- La orden supera esta comprobación cuando controlCalidadValidado es verdadero.
- Se impide finalizarla cuando ese valor es falso.

##### R.N.09. Factura obligatoria para cerrar
Una orden no puede pasar a Cerrada y Facturada sin una factura asociada.

**Pruebas de aceptación:**
- Una orden con factura supera esta comprobación.
- Se impide cerrar una orden sin factura.

##### R.N.10. Kilometraje de salida obligatorio
Una orden no puede pasar a Cerrada y Facturada sin kilometraje de salida registrado.

**Pruebas de aceptación:**
- Con kilometraje de salida registrado se supera esta comprobación.
- Se impide cerrar la orden si falta ese dato.

##### R.N.11. Cálculo del IVA del proyecto
En este ejercicio se aplica un tipo fijo del 21 %: la cuota es la base multiplicada por 0,21 y el total es la suma de base y cuota.

**Pruebas de aceptación:**
- Para una base de 100 euros se obtiene una cuota de 21 y un total de 121.
- Se rechaza un total que no coincida con la base y la cuota calculada.

##### R.N.12. Caducidad del presupuesto
Un presupuesto que siga pendiente después de 15 días naturales desde su emisión pasa a Caducado.

**Pruebas de aceptación:**
- Con exactamente 15 días el presupuesto pendiente no se marca como caducado.
- Con 16 días se marca como caducado.

### 4.2. Mapa de historias de usuario

Los RG están en la fila superior; debajo aparecen los RF que desarrollan cada objetivo.

| **RG01. Gestionar la información de clientes y vehículos** | **RG02. Planificar y controlar el trabajo del taller** | **RG03. Controlar el inventario de repuestos** | **RG04. Gestionar presupuestos, facturación y cobros** |
| :--- | :--- | :--- | :--- |
| **RF01.** Historial por vehículo | **RF02.** Órdenes y carga por mecánico | **RF03.** Stock crítico por familia | **RF04.** Presupuestos por cliente |
| **RF06.** Búsqueda y actividad | **RF09.** Costes y márgenes | | **RF05.** Facturación y saldos |
| **RF07.** Reparaciones recurrentes | | | |
| **RF08.** ITV y revisión | | | |

### 4.3. Requisitos no funcionales

#### R.N.F.01. Rendimiento de las consultas
Como **Mecánico**, quiero **que las consultas habituales respondan en menos de 1,5 segundos en condiciones de concurrencia habitual**, para **evitar esperas durante el trabajo**.

#### R.N.F.02. Usabilidad
Como **Mecánico**, quiero **una interfaz adaptable a tabletas, con texto legible y controles amplios**, para **consultarla cómodamente en el taller**.

#### R.N.F.03. Seguridad de acceso
Como **Mecánico**, quiero **acceder mediante una cuenta individual y que las consultas y modificaciones requieran identificarse**, para **evitar accesos no autorizados**. Todos los mecánicos tienen el mismo perfil.

#### R.N.F.04. Copias de seguridad
Como **Mecánico**, quiero **una copia automática diaria de la base de datos y un procedimiento de restauración**, para **recuperar la información tras un fallo**.

## 5. Modelo conceptual

### 5.1. Diagrama de clases y decisiones

Se representan **nueve clases**: Cliente, Vehículo, Orden de trabajo, Mecánico, Intervención, Repuesto, Uso de repuesto, Presupuesto y Factura. Los RI describen los datos de este modelo y el único perfil de usuario es Mecánico.

[Ver el modelo conceptual en PDF](Modelo_y_trazabilidad.pdf)

**Lectura:** `1` significa exactamente uno; `0..1`, ninguno o uno; `0..*`, ninguno o varios. `[0..1]` marca un atributo opcional. No se incluyen métodos ni tipos de programación.

**Intervención** permite registrar varios trabajos en una orden. Cada intervención la realiza un mecánico; una orden puede tener varios mecánicos asignados antes de que comiencen los trabajos.

**Uso de repuesto** se dibuja como una **clase asociación**, unida con una línea discontinua a la relación Orden-Repuesto. Guarda las unidades, el estado (Reservado o Consumido) y los precios aplicados. Para simplificar, cada pareja orden-repuesto se resume en un único uso con una cantidad, un estado y unos precios comunes.

La **Factura** se asocia directamente al cliente destinatario y a una orden. Se propone como máximo una factura por orden. Se guarda el importe cobrado acumulado; no se modela cada pago por separado. El saldo y el estado de pago se obtienen a partir de ese importe y del total.

#### Asociaciones

| Código | Asociación | Multiplicidades en el orden de las clases |
| :--- | :--- | :---: |
| A01 | Cliente posee Vehículo | `1 / 0..*` |
| A02 | Vehículo registra Orden de trabajo | `1 / 0..*` |
| A03 | Orden de trabajo incluye Intervención | `1 / 0..*` |
| A04 | Mecánico realiza Intervención | `1 / 0..*` |
| A05 | Orden de trabajo tiene asignado Mecánico | `0..* / 0..*` |
| A06 | Orden de trabajo utiliza Repuesto | `0..* / 0..*` |
| A07 | Orden de trabajo tiene Presupuesto | `1 / 0..*` |
| A08 | Orden de trabajo genera Factura | `1 / 0..1` |
| A09 | Cliente recibe Factura | `1 / 0..*` |

La clase asociación **Uso de repuesto** forma parte de A06. Los códigos A01-A09 sirven para localizar asociaciones en las matrices; no son requisitos adicionales.

### 5.2. Restricciones

Las notas C01-C07 se leen junto al diagrama. Cada una se vincula con las RN de esta versión.

**{C01. Identificadores: El NIF/CIF de cliente, la matrícula y el VIN son únicos. El VIN tiene 17 caracteres alfanuméricos en mayúsculas, sin I, O ni Q.}**  
Origen: RN01-RN04.

**{C02. Autorización: Una orden solo inicia la reparación con un presupuesto aceptado o una autorización directa expresa, con límite de importe registrado y respetado.}**  
Origen: RN05.

**{C03. Reservas: Disponible = stock físico - suma de unidades con estado Reservado. No se reserva más de lo disponible. Reservar no reduce el stock físico; consumir descuenta las unidades una vez y libera la reserva.}**  
Origen: RN06.

**{C04. Finalización: Para pasar a Finalizada debe existir al menos una intervención con horas de trabajo y estar registrado el control de calidad validado por el mecánico.}**  
Origen: RN07-RN08.

**{C05. Cierre: Para pasar a Cerrada y Facturada deben existir una factura asociada y un kilometraje de salida registrado.}**  
Origen: RN09-RN10.

**{C06. Importes: Se utiliza el 21 % fijado para este ejercicio: cuotaIVA = baseImponible × 0,21; total = baseImponible + cuotaIVA. En Factura, saldoPendiente = total - importeCobrado. El estado de pago se obtiene del importe acumulado recibido.}**  
Origen: RN11; RI05-RI06.

**{C07. Caducidad: Si un presupuesto sigue pendiente y han pasado más de 15 días naturales desde su emisión, pasa a Caducado. Con exactamente 15 días todavía no caduca.}**  
Origen: RN12.

### 5.3. Escenarios de prueba y diagramas de objetos

Los datos son ficticios. Los diagramas son vistas parciales: un enlace omitido no significa que no exista. Los nombres `objeto : Clase` y los enlaces aparecen subrayados, como en el Tema 5.

#### E1. Presupuesto pendiente

Carmen trae su vehículo. La orden OT01 está Presupuestada y P01 sigue Pendiente. No hay autorización directa.

**Resultado esperado:** la situación es válida, pero se rechaza un intento de iniciar la reparación por **C02 / RN05**.

#### E2. Reserva superior al stock

Se intenta reservar 3 unidades para OT03, pero solo hay 2 unidades físicas y ninguna reserva anterior. El diagrama muestra la propuesta inválida que se rechaza, no un estado admitido por el sistema.

**Resultado esperado:** se rechaza por **C03 / RN06**. No se guarda el uso de 3 unidades y el stock físico sigue siendo 2.

#### E3. Reparación facturada con importe cobrado parcial

OT02 tiene autorización directa hasta 200 euros. Luis realiza 2 horas de trabajo, con tarifa de 40 euros y coste de 20 euros por hora. Se consume un repuesto con coste de 20 euros y venta de 30 euros. El mecánico registra el control de calidad, el kilometraje de salida y la factura. El importe cobrado acumulado es 100 euros.

| Cálculo | Resultado |
| :--- | ---: |
| Mano de obra: 2 × 40 | 80,00 euros |
| Base: 80 + 30 | 110,00 euros |
| Coste: 2 × 20 + 20 | 60,00 euros |
| Margen sin IVA: 110 - 60 | 50,00 euros |
| IVA del proyecto: 110 × 0,21 | 23,10 euros |
| Total: 110 + 23,10 | 133,10 euros |
| Saldo: 133,10 - 100 | 33,10 euros |

**Resultado esperado:** cumple **C04-C06**. La orden puede cerrarse con importe parcialmente cobrado, porque **RN09** exige factura pero no el pago completo. En esta vista no se muestran el cliente y el vehículo; sus enlaces existen. El stock mostrado ya refleja el consumo.

## 6. Matrices de trazabilidad

### 6.1. Requisitos de información, clases y asociaciones

**X** indica que el elemento representa información del RI. Los requisitos se han ajustado al alcance descrito en el modelo.

| RI | Cliente | Vehículo | Orden | Mecánico | Intervención | Repuesto | Uso | Factura | Presupuesto | Asociaciones |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| RI01 | X | - | - | - | - | - | - | - | - | - |
| RI02 | X | X | - | - | - | - | - | - | - | A01 |
| RI03 | - | X | X | X | X | X | X | - | - | A02, A03, A04, A05, A06 |
| RI04 | - | - | X | - | - | X | X | - | - | A06 |
| RI05 | X | - | X | - | - | - | - | X | - | A08, A09 |
| RI06 | - | - | X | - | - | - | - | - | X | A07 |

### 6.2. Reglas de negocio y restricciones

**X** identifica la nota que recoge cada regla. Solo aparecen las reglas incluidas en el catálogo actual.

| Regla | C01 | C02 | C03 | C04 | C05 | C06 | C07 |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| RN01. NIF/CIF de cliente único | X | - | - | - | - | - | - |
| RN02. Matrícula única | X | - | - | - | - | - | - |
| RN03. VIN único | X | - | - | - | - | - | - |
| RN04. Formato del VIN | X | - | - | - | - | - | - |
| RN05. Autorización previa | - | X | - | - | - | - | - |
| RN06. Reservas limitadas al stock disponible | - | - | X | - | - | - | - |
| RN07. Intervención necesaria para finalizar | - | - | - | X | - | - | - |
| RN08. Control de calidad validado | - | - | - | X | - | - | - |
| RN09. Factura obligatoria para cerrar | - | - | - | - | X | - | - |
| RN10. Kilometraje de salida obligatorio | - | - | - | - | X | - | - |
| RN11. Cálculo del IVA del proyecto | - | - | - | - | - | X | - |
| RN12. Caducidad del presupuesto | - | - | - | - | - | - | X |

**Elementos afectados por las restricciones:**

| Nota | Clases y asociaciones |
| :--- | :--- |
| C01 | Cliente y Vehículo |
| C02 | Orden, Presupuesto y A07 |
| C03 | Orden, Repuesto, Uso y A06 |
| C04 | Orden, Intervención y A03 |
| C05 | Orden, Factura y A08 |
| C06 | Factura y Presupuesto |
| C07 | Presupuesto |

### 6.3. Cruces de datos para las consultas

Esta tabla ayuda a explicar los RF; no introduce requisitos nuevos.

| RF | Datos relacionados | Operación |
| :--- | :--- | :--- |
| RF01 | Vehículo - Orden - Intervención / Factura | Recuento de órdenes, suma de horas e importes por año. |
| RF02 | Orden - Mecánico - Intervención | Órdenes asignadas y horas por mecánico. |
| RF03 | Orden - Uso de repuesto - Repuesto | Disponible y necesidades agrupadas por familia. |
| RF04 | Presupuesto - Orden - Vehículo - Cliente | Recuento, suma de presupuestos y días transcurridos. |
| RF05 | Cliente - Factura | Suma, media, recuento y saldo por mes y cliente. |
| RF06 | Cliente - Vehículo - Orden | Recuentos y fecha de la última reparación. |
| RF07 | Vehículo - Orden - Intervención / Factura | Comparar componentes, fechas y kilometraje; contar repeticiones. |
| RF08 | Cliente - Vehículo | Agrupar próximos vencimientos por semana y tipo. |
| RF09 | Orden - Intervención / Uso - Repuesto | Sumar costes y ventas; obtener el margen sin IVA. |

Al calcular costes e ingresos, se obtienen primero los subtotales de horas y materiales por orden y después se combinan, para no sumar varias veces la misma factura o intervención.

**Base de trabajo:** primer entregable del grupo, indicaciones de simplificación y Tema 5 de IISSI (notación de clases, clases asociación, restricciones, objetos y trazabilidad). Los escenarios son ejemplos ficticios del proyecto.
