# TALLER MECÁNICO TREVOR'S MOTORS

## Miembros del grupo LX-XXX-X (sustituir)

1. Macías Verdugo, Jose Javier
2. García Bermúdez, Raúl
3. Romero de la Osa Díaz, Adrián


## 1. Introducción al problema

El proyecto consiste en el desarrollo de un sistema de gestión para un taller de reparación y mantenimiento de vehículos. El objetivo principal del sistema será facilitar la gestión de la información relacionada con los clientes, sus vehículos, las citas y las reparaciones realizadas en el taller.
Actualmente, en un taller de estas características, parte de la información puede gestionarse mediante anotaciones, documentos físicos, hojas de cálculo u otras herramientas. Esta situación puede dificultar la consulta y actualización de la información, especialmente cuando aumenta el número de clientes y vehículos. También puede resultar complicado realizar un seguimiento adecuado de las reparaciones realizadas y del historial de cada vehículo.
El taller atiende principalmente a clientes particulares que llevan sus vehículos para realizar revisiones, mantenimientos o reparaciones. Un mismo vehículo puede acudir al taller en diferentes ocasiones y recibir distintos servicios, por lo que resulta útil disponer de un historial de las intervenciones realizadas.
El sistema será utilizado por los empleados del taller, que podrán gestionar la información de los clientes y sus vehículos, organizar las citas, registrar las reparaciones y consultar el historial de intervenciones realizadas.
Con el desarrollo del sistema se pretende centralizar y organizar la información del taller, reducir los errores derivados de la gestión manual y facilitar el acceso a la información necesaria para realizar el trabajo diario.
El sistema se centrará inicialmente en operaciones habituales de un taller generalista, como revisiones, cambios de aceite y filtros, sustitución de frenos, cambio de neumáticos, baterías y otras reparaciones mecánicas habituales.

<img width="1024" height="572" alt="image" src="https://github.com/user-attachments/assets/a18a948e-2fad-42d3-afc1-cfe1795a0af3" />


## 2. Glosario de términos

- Términos específicos del dominio del problema, ordenados alfabéticamente. Se valorará la presencia de información multimedia.

## 3. Visión general del sistema

### 3.1. Requisitos generales

### 3.2. Usuarios del sistema

## 4. Catálogo de requisitos

### 4.1. Requisitos funcionales

#### R.F.01. Título requisito funcional

Como [tipo de usuario]
quiero [servicio]
para [razón]

**Prueba de aceptación**
- Descripción de la primera comprobación a realizar
- Descripción de la segunda comprobación a realizar
- Se debe aplicar la regla de negocio R.N.XX.
- ...

#### 4.1.1. Requisitos de información

##### R.I.01. Título requisito de información

Como [tipo de usuario]
quiero [servicio]
para [razón]

**Prueba de aceptación**
- Descripción de la primera comprobación a realizar
- Descripción de la segunda comprobación a realizar
- ...

#### 4.1.2. Reglas de negocio

##### R.N.01. Título regla negocio

Descripción de la regla de negocio.

### 4.2. Mapa de historias de usuario (opcional)

### 4.3. Requisitos no funcionales (opcional)

**R.N.F. 01. Título requisito no funcional**
Como [tipo de usuario]
quiero [servicio]
para [razón]

-- fin entregable 1 --

## 5. Modelo conceptual

### 5.1. Diagramas de clases UML

- con restricciones.

### 5.2. Escenarios de prueba

- con descripción textual y diagrama de objetos UML.

## 6. Matrices de trazabilidad

- Matriz de trazabilidad entre los elementos del modelo conceptual y los requisitos.

|       | EntidadX   | AsociaciónX  | RestricciónX  | Entidad2 ...   | 
|:------|:-----------|:-----------|:-----------|:-----------|
| RI-1  | X          | X          | X          | X          |
| RI-2  |            | X          |            | X          |
| RF-1  |            | X          |            | X          |
| RF-2  | X          |            | X          | X          |
| RN-1  |            | X          |            |            |
| RN-2  | X          | X          | X          |            |
| ...   |            |            |            |            |

-- fin entregable 2 --

## 7. Modelo relacional en 3FN

- Relaciones obtenidas al aplicar la transformación del modelo conceptual.

### 7.1.  Justificación de la estrategia de transformación de jerarquías

- si se identificaron jerarquías en el MC.


### 8. Matriz de trazabilidad MC/SQL (opcional):

- Restricciones sobre el MC / Elementos del modelo tecnológico (SQL) (Triggers, checks, etc.)
- Incluir Reglas de negocio — Constraints/Triggers en las matrices de trazabilidad para el entregable 3

|       | EntidadX   | AsociaciónX  | RestricciónX  | Entidad2 ...   | 
|:-------|:-------|:-------|:-------|:-------|
| TABLA-1 |        |        |        |        |
| TABLA-2 |        |        |        |        |
| TABLA-3 |        |        |        |        |
| TABLA-4 |        |        |        |        |
| TRIG-1 |        |        |        |        |
| TRIG-2 | X      | X      |        | X      |
| TRIG-3 |        | X      |        | X      |
| TRIG-4 |        |        | X      |        |
| CONST-1 |        |        |        |        |
| CONST-2 | X      | X      |        | X      |
| CONST-3 |        | X      |        | X      |
| CONST-4 |        |        | X      |        |

Se consideran todo tipo de constraints declarativas (aquellas definidas durante el CREATE TABLE).
-- fin entregable 3 --

## Referencias


