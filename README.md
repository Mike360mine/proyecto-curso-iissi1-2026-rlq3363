# Base de datos para una empresa de transporte.
<!-- Añadir aquí una imagen de portada (logo inventado de TransRuta, foto de una estación, un autobús o un tren...) -->

## Miembros del grupo L5-AM-8
| Integrantes |  |
| :--- | :---: |
| 1. Morilla Rodríguez, Jorge <br><br>2. Apellidos, Nombre <br><br>3. Diaz Aguilar, Miguel <br><br>4. Apellidos, Nombre | <!-- <img src="foto_grupo.jpg" height="300" alt="Grupo"> --> |


---

## Índice
- [Base de datos para una empresa de transporte.](#base-de-datos-para-una-empresa-de-transporte)
  - [Miembros del grupo L5-AM-8](#miembros-del-grupo-lx-xxx-x)
- [1. Introducción al problema](#1-introducción-al-problema)
  - [Contexto del problema](#contexto-del-problema)
  - [Problemática actual](#problemática-actual)
  - [Expectativas del sistema](#expectativas-del-sistema)
- [2. Glosario de términos](#2-glosario-de-términos)
- [3. Visión general del sistema](#3-visión-general-del-sistema)
  - [3.1. Requisitos generales](#31-requisitos-generales)
  - [3.2. Usuarios del sistema](#32-usuarios-del-sistema)
- [4. Catálogo de requisitos](#4-catálogo-de-requisitos)
  - [4.1. Requisitos funcionales](#41-requisitos-funcionales)
      - [4.1.1. Requisitos de información](#411-requisitos-de-información)
      - [4.1.2. Reglas de negocio](#412-reglas-de-negocio)
  - [4.2. Mapa de historias de usuario](#42-mapa-de-historias-de-usuario)
  - [4.3. Requisitos no funcionales (opcional)](#43-requisitos-no-funcionales-opcional)
- [5. Modelo conceptual](#5-modelo-conceptual)
    - [5.1. Diagramas de clases UML](#51-diagramas-de-clases-uml)
    - [5.2. Escenarios de prueba](#52-escenarios-de-prueba)
- [6. Matrices de trazabilidad](#6-matrices-de-trazabilidad)
- [7. Modelo relacional en 3FN](#7-modelo-relacional-en-3fn)
  - [7.1. Justificación de la estrategia de transformación de jerarquías](#71-justificación-de-la-estrategia-de-transformación-de-jerarquías)
- [8. Matriz de trazabilidad MC/SQL](#8-matriz-de-trazabilidad-mcsql-opcional)
- [9. Scripts](#9-scripts)
  - [9.1. Script de tablas y restricciones](1.scripts_tablas_restricciones.sql)
  - [9.2. Script de funciones y procedimientos](2.scripts_funciones_procedimientos.sql)
  - [9.3. Script de cursores y consultas](3.scripts_cursores_consultas.sql)
  - [9.4. Script de triggers](4.scripts_triggers.sql)
  - [9.5. Script de poblado de tablas](5.script_poblado_tablas.sql)

---
---


## 1. Introducción al problema

### Contexto del problema

TransRuta es una empresa de transporte interurbano que opera líneas de autobús y de tren. Cada estación de la red está supervisada por una central de control, donde trabajan controladores organizados por turnos. Los controladores vigilan el paso de los viajes por las estaciones de su central: anotan la hora real de llegada, detectan los retrasos y reportan las incidencias (averías, accidentes, problemas meteorológicos...). Cada viaje lo realiza un conductor habilitado con un vehículo de la flota, y los clientes compran billetes para un viaje concreto, entre una parada de origen y otra de destino de la línea.

La empresa necesita una nueva base de datos que centralice la red (centrales, estaciones, líneas y paradas), la flota, el personal, la planificación de viajes, la venta de billetes y el control operativo.

- **Usuario**: DNI, Nombre, Apellido1, Apellido2, Fecha de Nacimiento, Teléfono, Email, Contraseña, Nombre de Usuario. (Clase Padre).
- **Cliente**: Fecha de Registro, Tarifa (Enum). (Hereda de Usuario).
- **Empleado**: Nº Empleado, Fecha de Ingreso, Salario. (Hereda de Usuario).
- **Controlador**: Turno (Enum). (Hereda de Empleado).
- **Conductor**: Nº Licencia, Habilitación (Enum), Fecha de Caducidad de la Licencia. (Hereda de Empleado).
- **Administrador**: Departamento. (Hereda de Empleado).
- **Central**: Código, Nombre, Ciudad, Dirección, Teléfono.
- **Estación**: Código, Nombre, Ciudad, Dirección, Tipo (Enum), Nº Andenes.
- **Línea**: Código, Nombre, Tipo (Enum), Precio por Km, Activa (Boolean).
- **Parada**: Orden, Minutos desde Origen, Km desde Origen.
- **Vehículo**: Código, Tipo (Enum), Matrícula, Modelo, Capacidad, Fecha de Adquisición, Estado (Enum).
- **Viaje**: Código, Fecha/Hora de Salida, Fecha/Hora de Llegada Prevista, Estado (Enum).
- **Paso por estación**: Fecha/Hora Prevista (derivada), Fecha/Hora Real, Andén, Retraso (derivado).
- **Billete**: Código, Fecha/Hora de Compra, Asiento, Método de Pago (Enum), Tarifa (Enum), Precio, Estado (Enum).
- **Incidencia**: ID, Fecha/Hora, Tipo (Enum), Gravedad (Enum), Descripción, Resuelta (Boolean).


### Problemática actual

- **Información dispersa**: Los horarios, las asignaciones de conductores y vehículos y la venta de billetes se gestionan en sistemas separados (hojas de cálculo, una taquilla antigua y partes en papel), que no se comunican entre sí.
- **Control operativo manual**: Los controladores apuntan a mano o por radio los pasos de los viajes y las incidencias, de modo que no existen estadísticas fiables de puntualidad y los retrasos se detectan tarde.
- **Errores de planificación**: Se llegan a asignar conductores sin la habilitación adecuada o con la licencia caducada, vehículos que están en el taller y conductores o vehículos a dos viajes que se solapan.
- **Problemas en la venta**: No se comprueba la capacidad del vehículo al vender, lo que provoca overbooking; cuando se cancela un viaje, la anulación de billetes y los reembolsos se hacen uno a uno.
- **Seguridad y cumplimiento**: Los datos personales de clientes y empleados no están cifrados, los permisos son poco detallados y existe riesgo de incumplir la normativa de protección de datos.

### Expectativas del sistema

- **Centralización**: Una única base de datos fiable con toda la red, la flota, el personal, los viajes y las ventas.
- **Planificación sin errores**: El sistema valida automáticamente las asignaciones de conductores y vehículos a los viajes.
- **Venta controlada**: Los billetes se venden respetando el aforo y las condiciones de la línea, con el precio calculado automáticamente según el tramo y la tarifa del cliente.
- **Control operativo en tiempo real**: Los controladores registran los pasos y las incidencias desde la central; los retrasos se calculan y notifican solos.
- **Información para la gestión**: Informes de ocupación, puntualidad, ingresos y averías para tomar decisiones.

---
---

## 2. Glosario de términos

- **Andén**: Zona de una estación donde los vehículos se detienen para que suban y bajen los viajeros. Cada estación tiene un número de andenes y cada paso se asigna a uno de ellos.

- **Billete**: Título de transporte que da derecho a un cliente a viajar en un viaje concreto, entre una parada de origen y otra de destino.

- **Central de control**: Centro desde el que los controladores supervisan un conjunto de estaciones de la red.

- **Cliente**: Usuario registrado que consulta viajes y compra billetes.

- **Conductor**: Empleado encargado de conducir los vehículos en los viajes. Tiene una licencia con fecha de caducidad y una habilitación para un tipo de transporte.

- **Controlador**: Empleado que trabaja en una central de control durante un turno, anota los pasos de los viajes por las estaciones y reporta incidencias.

- **Empleado**: Usuario que trabaja para la empresa (controlador, conductor o administrador).

- **Estación**: Instalación de la red donde paran los vehículos. Puede ser de tipo AUTOBUS, TREN o INTERMODAL.

- **Estado (billete)**: Situación de un billete:
  VALIDO, USADO, ANULADO.

- **Estado (vehículo)**: Disponibilidad de un vehículo de la flota:
  OPERATIVO, EN_MANTENIMIENTO, AVERIADO, BAJA.

- **Estado (viaje)**: Fase en la que se encuentra un viaje:
  PROGRAMADO, EN_CURSO, FINALIZADO, CANCELADO.

- **Gravedad (incidencia)**: Nivel de impacto de una incidencia:
  LEVE, MODERADA, GRAVE, MUY_GRAVE.

- **Habilitación**: Tipo de transporte que un conductor está autorizado a conducir:
  AUTOBUS o TREN.

- **Incidencia**: Suceso que afecta al servicio normal de un viaje o de una estación. Puede ser de tipo AVERIA, RETRASO, ACCIDENTE, METEOROLOGICA, SEGURIDAD u OTRA.

- **Intermodal (estación)**: Estación en la que paran tanto líneas de autobús como de tren, permitiendo transbordos entre ambos medios.

- **Línea**: Recorrido fijo, de autobús o de tren, formado por una secuencia ordenada de paradas.

- **Método de pago**: Forma en la que se paga un billete:
  TARJETA, EFECTIVO, APP_MOVIL.

- **Overbooking**: Venta de más billetes que plazas tiene el vehículo asignado a un viaje.

- **Parada**: Posición de una estación dentro de una línea, con su orden, los minutos y los kilómetros desde el origen.

- **Paso por estación**: Registro de la llegada de un viaje a una estación de su línea, con la hora prevista, la hora real y el andén.

- **Retraso**: Diferencia en minutos entre la hora real y la hora prevista de un paso por estación.

- **Tarifa**: Categoría del cliente que determina el descuento aplicado al precio del billete; la tarifa vigente en el momento de la compra queda guardada en el billete:
  GENERAL, ESTUDIANTE, JUBILADO, INFANTIL.

- **Tipo de estación**: Clasificación de una estación según los medios que la usan:
  AUTOBUS, TREN, INTERMODAL.

- **Tipo de transporte**: Medio con el que opera una línea o un vehículo:
  AUTOBUS, TREN.

- **Turno**: Franja horaria de trabajo de un controlador:
  MAÑANA, TARDE, NOCHE.

- **Usuario**: Persona con credenciales de acceso al sistema (email, contraseña y nombre de usuario).

- **Vehículo**: Autobús o tren de la flota de la empresa, con una capacidad máxima de viajeros.

- **Viaje**: Realización concreta de una línea en una fecha y hora, con un vehículo y un conductor asignados.


----
----

## 3. Visión general del sistema

### 3.1. Requisitos generales

**1 — Consulta y compra de billetes**

Como cliente quiero consultar los viajes disponibles y comprar billetes de forma sencilla para planificar mis desplazamientos.
- Búsqueda de viajes por estación de origen, destino y fecha.
- Compra de billetes con el precio calculado según el tramo y la tarifa.
- Consulta y anulación de los billetes comprados.

**2 — Control operativo de la red**

Como controlador quiero registrar el paso de los viajes por las estaciones de mi central y las incidencias que se produzcan para mantener el servicio bajo control.
- Registro de la hora real de paso y del andén.
- Cálculo automático del retraso.
- Registro y seguimiento de incidencias.

**3 — Gestión de la red y de los recursos**

Como administrador quiero gestionar las líneas, estaciones, vehículos, empleados y viajes para planificar correctamente el servicio.
- Alta y modificación de centrales, estaciones, líneas y paradas.
- Asignación de conductores y vehículos a los viajes con validación automática.
- Informes de ocupación, puntualidad, ingresos y averías.

### 3.2. Usuarios del sistema

**Cliente**
- Consulta los viajes disponibles entre dos estaciones.
- Compra billetes y consulta su historial.
- Anula sus billetes antes de la salida del viaje.

**Controlador**
- Consulta los pasos previstos en las estaciones de su central.
- Registra la hora real de paso y el andén de cada viaje.
- Reporta incidencias y las marca como resueltas.
- Solo puede actuar sobre las estaciones de su central y los viajes que pasan por ellas.

**Conductor**
- Consulta su agenda de viajes y el vehículo asignado a cada uno.
- Consulta las paradas y los horarios de las líneas que conduce.

**Administrador**
- Da de alta y actualiza centrales, estaciones, líneas, paradas, vehículos y empleados.
- Programa los viajes y les asigna conductor y vehículo.
- Cancela viajes cuando es necesario.
- Elabora informes de ocupación, puntualidad, ingresos y averías.

---
---

## 4. Catálogo de requisitos

### 4.1. Requisitos funcionales

**RF.01 - Búsqueda de viajes disponibles**

Como cliente quiero consultar los viajes programados entre dos estaciones en una fecha determinada, con su hora de salida, hora de llegada y plazas libres, para elegir el que mejor me convenga.

Pruebas de aceptación:
- El sistema devuelve solo viajes en estado PROGRAMADO cuya línea pase por la estación de origen antes que por la de destino.
- Se muestran la hora de paso por el origen, la hora de llegada al destino, el precio del tramo y las plazas libres (capacidad del vehículo menos billetes VALIDOS).
- Los resultados se ordenan por hora de salida.
- Si no hay viajes, el sistema muestra un mensaje indicándolo.

---

**RF.02 - Historial de billetes del cliente**

Como cliente quiero consultar todos los billetes que he comprado, con su viaje, tramo, precio y estado, para llevar un control de mis viajes y gastos.

Pruebas de aceptación:
- El sistema muestra únicamente los billetes del cliente autenticado.
- Se muestran el código, la línea, la fecha del viaje, las paradas de origen y destino, el precio y el estado del billete.
- Se puede filtrar por estado (VALIDO, USADO, ANULADO) y por rango de fechas.
- Se muestra el gasto total del periodo filtrado, sin contar los billetes ANULADOS.

---

**RF.03 - Agenda del conductor**

Como conductor quiero consultar los viajes que tengo asignados en una semana, con la línea, el horario y el vehículo, para organizar mi jornada.

Pruebas de aceptación:
- El sistema muestra solo los viajes del conductor autenticado en el rango de fechas indicado.
- Para cada viaje se muestran la línea, la hora de salida, la hora de llegada prevista, el vehículo (código y modelo) y el estado.
- Los viajes CANCELADOS aparecen marcados como tales.
- Los resultados se ordenan cronológicamente.

---

**RF.04 - Panel de pasos de la central**

Como controlador quiero listar los pasos previstos para hoy en las estaciones de mi central, indicando cuáles se han registrado y con qué retraso, para supervisar el servicio en tiempo real.

Pruebas de aceptación:
- El sistema muestra solo los pasos de estaciones supervisadas por la central del controlador.
- Para cada paso se muestran la estación, la línea, el viaje, la hora prevista, la hora real (si existe), el andén y el retraso.
- Los pasos con más de 15 minutos de retraso aparecen destacados.
- Se puede filtrar por estación y por línea.

---

**RF.05 - Incidencias abiertas de la central**

Como controlador quiero listar las incidencias no resueltas de las estaciones y viajes de mi central, ordenadas por gravedad, para priorizar su resolución.

Pruebas de aceptación:
- El sistema muestra solo las incidencias con `resuelta = false` asociadas a estaciones de la central o a viajes que pasan por ellas.
- Se muestran el tipo, la gravedad, la fecha, la descripción, el controlador que la reportó y el viaje, estación o vehículo afectados.
- Los resultados se ordenan por gravedad (de MUY_GRAVE a LEVE) y después por fecha.
- Se puede filtrar por tipo de incidencia.

---

**RF.06 - Ocupación de los viajes de una línea**

Como administrador quiero consultar el porcentaje de ocupación de los viajes de una línea en un periodo para ajustar la oferta a la demanda.

Pruebas de aceptación:
- El sistema calcula la ocupación como billetes VALIDOS o USADOS entre la capacidad del vehículo asignado.
- Se muestran el código del viaje, la fecha, el vehículo, los billetes vendidos y el porcentaje de ocupación.
- Se muestra la ocupación media de la línea en el periodo.
- Los viajes CANCELADOS no se incluyen en el cálculo.

---

**RF.07 - Puntualidad por línea**

Como administrador quiero obtener el retraso medio de cada línea en un mes para detectar las líneas con más problemas de puntualidad.

Pruebas de aceptación:
- El sistema calcula el retraso medio a partir de los pasos con hora real registrada.
- Se muestran, por línea, el número de viajes, el retraso medio y el porcentaje de pasos con más de 15 minutos de retraso.
- Las líneas se ordenan de mayor a menor retraso medio.
- Se puede filtrar por tipo de transporte (AUTOBUS o TREN).

---

**RF.08 - Ingresos por línea y tarifa**

Como administrador quiero consultar los ingresos obtenidos por cada línea en un periodo, desglosados por la tarifa aplicada en cada billete, para analizar la rentabilidad del servicio.

Pruebas de aceptación:
- El sistema suma el precio de los billetes VALIDOS y USADOS; los ANULADOS no cuentan.
- Se muestra el total por línea y su desglose por tarifa (GENERAL, ESTUDIANTE, JUBILADO, INFANTIL).
- Se muestra el top 5 de líneas con más ingresos del periodo.
- Se puede filtrar por método de pago.

---

**RF.09 - Vehículos con más averías**

Como administrador quiero listar los vehículos que han tenido incidencias de tipo AVERIA en el último año, junto con su estado actual, para planificar el mantenimiento y las renovaciones de la flota.

Pruebas de aceptación:
- El sistema muestra el código, el tipo, el modelo, la fecha de adquisición, el estado actual y el número de averías de cada vehículo.
- Los resultados se ordenan por número de averías de mayor a menor.
- Se indica si el vehículo tiene alguna avería sin resolver.
- Se puede filtrar por tipo de vehículo y por estado.

---

**RF.10 - Conductores con licencia próxima a caducar**

Como administrador quiero listar los conductores cuya licencia caduca en los próximos 60 días, junto con los viajes que tienen asignados después de esa fecha, para evitar asignaciones no válidas.

Pruebas de aceptación:
- El sistema muestra el nombre, el nº de empleado, el nº de licencia, la habilitación y la fecha de caducidad.
- Para cada conductor se listan los viajes PROGRAMADOS posteriores a la caducidad de su licencia.
- Los resultados se ordenan por fecha de caducidad.


---
---


#### 4.1.1. Requisitos de información

**R.I.01 - Información sobre usuarios y roles**
Como administrador, quiero disponer de la siguiente información sobre los usuarios del sistema:
- **Datos Generales (Usuario):**
    - DNI
    - Nombre
    - Apellido 1
    - Apellido 2
    - Fecha de nacimiento
    - Teléfono
    - Email
    - Contraseña
    - Nombre de Usuario
- **Datos Específicos (Empleado):**
    - Número de Empleado
    - Fecha de Ingreso
    - Salario
- **Datos Específicos (Administrador):**
    - Departamento
- **Relaciones (Administrador):**
    * Gestiona: Línea (*)

Pruebas de aceptación:
- La búsqueda por DNI, email o nombre de usuario devuelve los datos del usuario y su rol (cliente, controlador, conductor o administrador).
- No se exponen contraseñas; los datos sensibles aparecen enmascarados en listados y exportaciones.
- No se admiten DNI, emails ni nombres de usuario duplicados.

---

**R.I.02 - Información sobre clientes**
Como administrador, quiero disponer de la siguiente información específica sobre los clientes:
- Fecha de registro
- Tarifa
- **Relaciones:**
    * Compra: Billete (*) (Relación inversa de "Billete comprado por Cliente")
    * Herencia: Es un Usuario

Pruebas de aceptación:
- Dado un cliente, se muestran sus datos personales, su tarifa y el número de billetes comprados.
- La tarifa JUBILADO exige que el cliente tenga al menos 65 años y la INFANTIL que tenga menos de 12, según su fecha de nacimiento.
- La fecha de registro no puede ser posterior a la fecha actual.

---

**R.I.03 - Información sobre controladores**
Como administrador, quiero disponer de la siguiente información específica sobre los controladores:
- Turno
- **Relaciones:**
    * Trabaja en: Central (1)
    * Anota: Paso por estación (*) (Relación inversa de "PasoEstacion anotado por Controlador")
    * Reporta: Incidencia (*) (Relación inversa de "Incidencia reportada por Controlador")
    * Herencia: Es un Empleado

Pruebas de aceptación:
- Se listan los controladores de una central agrupados por turno (MAÑANA, TARDE, NOCHE).
- Todo controlador está asignado a una única central.
- Para cada controlador se puede consultar el número de pasos anotados y de incidencias reportadas en un periodo.

---

**R.I.04 - Información sobre conductores**
Como administrador, quiero disponer de la siguiente información específica sobre los conductores:
- Número de licencia
- Habilitación
- Fecha de caducidad de la licencia
- **Relaciones:**
    * Conduce: Viaje (*) (Relación inversa de "Viaje conducido por Conductor")
    * Herencia: Es un Empleado

Pruebas de aceptación:
- Dado un número de licencia válido, se muestran los datos del conductor, su habilitación y la vigencia de la licencia.
- No se admiten números de licencia duplicados.
- Se listan los viajes asignados al conductor ordenados por fecha, sin solapamientos (según RN.03).

---

**R.I.05 - Información sobre centrales**
Como administrador, quiero disponer de la siguiente información sobre las centrales de control:
- Código
- Nombre
- Ciudad
- Dirección
- Teléfono
- **Relaciones:**
    * Supervisa: Estación (1..*)
    * Lugar de trabajo de: Controlador (1..*) (Relación inversa de "Controlador trabaja en Central")

Pruebas de aceptación:
- Se listan las centrales con el número de estaciones supervisadas y de controladores por turno.
- Toda central supervisa al menos una estación y tiene al menos un controlador.
- No se admiten códigos de central duplicados.

---

**R.I.06 - Información sobre estaciones**
Como controlador, quiero disponer de la siguiente información sobre las estaciones:
- Código
- Nombre
- Ciudad
- Dirección
- Tipo
- Número de andenes
- **Relaciones:**
    * Supervisada por: Central (1) (Relación inversa de "Central supervisa Estación")
    * Ubicación de: Parada (*) (Relación inversa de "Parada se sitúa en Estación")
    * Lugar de: Paso por estación (*) (Relación inversa de "PasoEstacion en Estación")
    * Lugar de: Incidencia (*) (Relación inversa de "Incidencia ocurre en Estación")

Pruebas de aceptación:
- Dada una estación, se muestran su central, su tipo y las líneas que paran en ella.
- Toda estación está supervisada por exactamente una central.
- El número de andenes es mayor que 0 y el andén de cada paso no puede superarlo.

---

**R.I.07 - Información sobre líneas**
Como administrador, quiero disponer de la siguiente información sobre las líneas:
- Código
- Nombre
- Tipo
- Precio por km
- Activa
- **Relaciones:**
    * Compuesta por: Parada (2..*)
    * Gestionada por: Administrador (1) (Relación inversa de "Administrador gestiona Línea")
    * Realizada en: Viaje (*) (Relación inversa de "Viaje pertenece a Línea")

Pruebas de aceptación:
- Dada una línea, se muestran sus paradas ordenadas, con la estación, los minutos y los km desde el origen.
- Toda línea tiene al menos dos paradas.
- El precio por km es mayor que 0; no se pueden programar viajes en líneas inactivas.

---

**R.I.08 - Información sobre paradas**
Como administrador, quiero disponer de la siguiente información sobre las paradas de cada línea:
- Orden
- Minutos desde el origen
- Km desde el origen
- **Relaciones:**
    * Pertenece a: Línea (1) (Relación inversa de "Línea compuesta por Parada")
    * Se sitúa en: Estación (1)
    * Origen de: Billete (*) (Relación inversa de "Billete origen Parada")
    * Destino de: Billete (*) (Relación inversa de "Billete destino Parada")

Pruebas de aceptación:
- El orden de las paradas de una línea es único y consecutivo empezando en 1.
- La primera parada tiene 0 minutos y 0 km desde el origen; los valores crecen con el orden.
- Una misma estación no puede aparecer dos veces en la misma línea.

---

**R.I.09 - Información sobre vehículos**
Como administrador, quiero disponer de la siguiente información sobre los vehículos de la flota:
- Código
- Tipo
- Matrícula
- Modelo
- Capacidad
- Fecha de adquisición
- Estado
- **Relaciones:**
    * Realiza: Viaje (*) (Relación inversa de "Viaje realizado con Vehículo")
    * Implicado en: Incidencia (*) (Relación inversa de "Incidencia implica a Vehículo")

Pruebas de aceptación:
- Dado un código válido, se muestran el tipo, el modelo, la capacidad, el estado y los próximos viajes asignados.
- Los autobuses deben tener matrícula; los trenes no la necesitan.
- La capacidad es mayor que 0 y no se admiten códigos ni matrículas duplicados.

---

**R.I.10 - Información sobre viajes**
Como administrador, quiero disponer de la siguiente información sobre los viajes:
- Código
- Fecha y hora de salida
- Fecha y hora de llegada prevista
- Estado
- **Relaciones:**
    * Pertenece a: Línea (1)
    * Realizado con: Vehículo (1)
    * Conducido por: Conductor (1)
    * Pasa por: Paso por estación (*) (Relación inversa de "PasoEstacion registra paso de Viaje")
    * Vende: Billete (*) (Relación inversa de "Billete para Viaje")
    * Afectado por: Incidencia (*) (Relación inversa de "Incidencia afecta a Viaje")

Pruebas de aceptación:
- La ficha del viaje muestra la línea, el conductor, el vehículo, los pasos por estación y el número de billetes vendidos.
- La llegada prevista es posterior a la salida.
- Cada cambio de estado queda registrado con fecha y usuario para auditoría.

---

**R.I.11 - Información sobre pasos por estación**
Como controlador, quiero disponer de la siguiente información sobre los pasos de los viajes por las estaciones:
- Fecha y hora prevista (derivada)
- Fecha y hora real
- Andén
- Retraso en minutos (derivado)
- **Relaciones:**
    * Registra paso de: Viaje (1)
    * En: Estación (1)
    * Anotado por: Controlador (0..1)

Pruebas de aceptación:
- La hora prevista se obtiene de la salida del viaje más los minutos desde el origen de la parada.
- Mientras no se registre la hora real, el paso no tiene controlador asociado y el retraso no se calcula.
- El retraso es la diferencia en minutos entre la hora real y la prevista.

---

**R.I.12 - Información sobre billetes**
Como cliente, quiero disponer de la siguiente información sobre los billetes:
- Código
- Fecha y hora de compra
- Asiento
- Método de pago
- Tarifa aplicada
- Precio
- Estado
- **Relaciones:**
    * Comprado por: Cliente (1)
    * Para: Viaje (1)
    * Origen: Parada (1)
    * Destino: Parada (1)

Pruebas de aceptación:
- Dado un código de billete válido, se muestran el cliente, el viaje, el tramo, el precio y el estado.
- En un mismo viaje no puede haber dos billetes VALIDOS con el mismo asiento.
- El precio y la tarifa se fijan en el momento de la compra según RN.06 y no cambian aunque después cambie la tarifa del cliente.

---

**R.I.13 - Información sobre incidencias**
Como controlador, quiero disponer de la siguiente información sobre las incidencias:
- ID
- Fecha y hora
- Tipo
- Gravedad
- Descripción
- Resuelta
- **Relaciones:**
    * Reportada por: Controlador (1)
    * Afecta a: Viaje (0..1)
    * Ocurre en: Estación (0..1)
    * Implica a: Vehículo (0..1)

Pruebas de aceptación:
- Toda incidencia está asociada al menos a un viaje o a una estación.
- Se pueden filtrar incidencias por tipo, gravedad, estado de resolución y rango de fechas.
- Una incidencia resuelta no puede volver a marcarse como no resuelta.

---
---


#### 4.1.2. Reglas de negocio

**RN.01- Habilitación del conductor**

Como administrador quiero que un conductor solo pueda conducir viajes de líneas del mismo tipo que su habilitación, que tenga la licencia en vigor en la fecha del viaje y al menos 21 años, para garantizar la seguridad del servicio.

Pruebas de aceptación:
- Se intenta asignar un conductor habilitado para AUTOBUS a un viaje de una línea de TREN; el sistema rechaza la asignación.
- Se intenta asignar un conductor cuya licencia caduca antes de la fecha del viaje; el sistema rechaza la asignación.
- Se asigna un conductor con la habilitación correcta y la licencia en vigor; el sistema acepta la asignación.

---

**RN.02- Compatibilidad del vehículo con la línea**

Como administrador quiero que el vehículo asignado a un viaje sea del mismo tipo que la línea y esté en estado OPERATIVO.

Pruebas de aceptación:
- Se intenta asignar un tren a un viaje de una línea de autobús; el sistema rechaza la asignación.
- Se intenta asignar un vehículo EN_MANTENIMIENTO, AVERIADO o de BAJA; el sistema rechaza la asignación.
- Se asigna un vehículo OPERATIVO del tipo correcto; el sistema acepta la asignación.

---

**RN.03- Prohibición de solapamientos**

Como administrador quiero que ni un conductor ni un vehículo puedan tener asignados dos viajes cuyos horarios se solapen.

Pruebas de aceptación:
- Se intenta asignar a un conductor un viaje que empieza antes de la llegada prevista de otro de sus viajes; el sistema rechaza la asignación.
- Se intenta asignar un vehículo a dos viajes solapados; el sistema rechaza la asignación.
- Se asigna un viaje que empieza después de la llegada del anterior; el sistema la acepta.

---

**RN.04- Control de aforo**

Como administrador quiero que el número de billetes VALIDOS de un viaje no pueda superar la capacidad del vehículo asignado, para evitar el overbooking.

Pruebas de aceptación:
- Se intenta vender un billete para un viaje con todas las plazas ocupadas; el sistema rechaza la venta.
- Se anula un billete de un viaje completo; el sistema permite vender una nueva plaza.

---

**RN.05- Condiciones de venta de billetes**

Como cliente quiero que solo se puedan comprar billetes de viajes PROGRAMADOS, antes de su hora de salida, y cuyas paradas de origen y destino pertenezcan a la línea del viaje, con el origen antes que el destino.

Pruebas de aceptación:
- Se intenta comprar un billete de un viaje EN_CURSO, FINALIZADO o CANCELADO; el sistema rechaza la compra.
- Se intenta comprar un billete con una parada que no pertenece a la línea del viaje; el sistema rechaza la compra.
- Se intenta comprar un billete cuyo origen tiene un orden mayor o igual que el destino; el sistema rechaza la compra.
- Se compra un billete válido; el sistema lo registra en estado VALIDO.

---

**RN.06- Cálculo del precio del billete**

Como administrador quiero que, en el momento de la compra, el precio del billete se calcule automáticamente como el precio por km de la línea multiplicado por los km entre origen y destino, aplicando el descuento de la tarifa que tenga el cliente en ese momento: ESTUDIANTE 20 %, JUBILADO 30 % e INFANTIL 50 %. La tarifa aplicada y el precio quedan guardados en el billete.

Pruebas de aceptación:
- Se compra un billete de 100 km en una línea de 0,10 €/km con tarifa GENERAL; el precio es 10,00 €.
- Se compra el mismo billete con tarifa JUBILADO; el precio es 7,00 €.
- Se intenta asignar la tarifa JUBILADO a un cliente menor de 65 años; el sistema rechaza el cambio.
- Se cambia la tarifa de un cliente después de una compra; el precio y la tarifa de ese billete no cambian.

---

**RN.07- Anulación automática por cancelación**

Como cliente quiero que, si se cancela un viaje, todos sus billetes VALIDOS pasen automáticamente a ANULADO para poder recibir el reembolso.

Pruebas de aceptación:
- Se cancela un viaje con billetes vendidos; el sistema cambia todos sus billetes VALIDOS a ANULADO.
- Se intenta cancelar un viaje FINALIZADO; el sistema rechaza la acción.

---

**RN.08- Ámbito de actuación del controlador**

Como administrador quiero que un controlador solo pueda anotar pasos y reportar incidencias en estaciones supervisadas por la central en la que trabaja o sobre viajes que pasan por ellas.

Pruebas de aceptación:
- Un controlador intenta registrar un paso en una estación de otra central; el sistema rechaza la acción.
- Un controlador registra un paso en una estación de su central; el sistema lo acepta.

---

**RN.09- Incidencia automática por retraso**

Como controlador quiero que, al registrar un paso con más de 15 minutos de retraso, el sistema genere automáticamente una incidencia de tipo RETRASO asociada al viaje y a la estación, reportada por el controlador que anota el paso.

Pruebas de aceptación:
- Se registra un paso con 20 minutos de retraso; el sistema crea una incidencia RETRASO no resuelta.
- Se registra un paso con 5 minutos de retraso; el sistema no crea ninguna incidencia.

---

**RN.10- Bloqueo de vehículos averiados**

Como administrador quiero que, al reportar una incidencia de tipo AVERIA que implique a un vehículo, este pase a estado AVERIADO y no pueda asignarse a nuevos viajes hasta que la incidencia se resuelva.

Pruebas de aceptación:
- Se reporta una avería de un vehículo; el sistema cambia su estado a AVERIADO.
- Se intenta asignar ese vehículo a un viaje; el sistema rechaza la asignación (según RN.02).
- Se resuelve la incidencia; el vehículo vuelve a estado OPERATIVO.

---

**RN.11- Compatibilidad de estaciones y líneas**

Como administrador quiero que las estaciones de tipo AUTOBUS solo puedan ser parada de líneas de autobús, las de tipo TREN solo de líneas de tren y las INTERMODAL de ambos tipos.

Pruebas de aceptación:
- Se intenta añadir una estación de TREN como parada de una línea de autobús; el sistema rechaza la acción.
- Se añade una estación INTERMODAL a una línea de autobús y a una de tren; el sistema acepta ambas.


---
---

### 4.2. Mapa de historias de usuario

**GESTIÓN DE LA RED**
* Configuración de la red
  - Gestionar centrales y estaciones
    * Dar de alta centrales de control
    * Registrar estaciones y asignarlas a una central
    * Definir el tipo y los andenes de cada estación
  - Gestionar líneas
    * Crear líneas de autobús y de tren
    * Definir las paradas y su orden
    * Activar o desactivar líneas

**GESTIÓN DE VIAJES**
* Planificación del servicio
  - Programar viajes
    * Crear viajes de una línea
    * Asignar conductor y vehículo
    * Cancelar viajes
  - Consultar la planificación
    * Agenda de los conductores
    * Horarios de cada línea

**VENTA DE BILLETES**
* Atención al cliente
  - Buscar viajes
    * Por origen, destino y fecha
    * Consultar plazas libres y precio
  - Gestionar billetes
    * Comprar billetes
    * Anular billetes
    * Consultar el historial

**CONTROL OPERATIVO**
* Supervisión de la red
  - Registrar pasos
    * Anotar la hora real y el andén
    * Consultar los retrasos
  - Gestionar incidencias
    * Reportar incidencias
    * Clasificar por tipo y gravedad
    * Marcar como resueltas

**GESTIÓN DE RECURSOS**
* Flota y personal
  - Gestionar la flota
    * Registrar vehículos
    * Actualizar su estado
    * Consultar las averías
  - Gestionar el personal y los usuarios
    * Dar de alta empleados y clientes
    * Asignar controladores a centrales y turnos
    * Controlar la vigencia de las licencias


---
---


### 4.3. Requisitos no funcionales (opcional)

*R.N.F. 01. Alta disponibilidad del sistema*

El sistema deberá garantizar una disponibilidad del 99.9 % anual, ya que la venta de billetes y el control operativo funcionan todos los días del año.

*R.N.F. 02. Capacidad de usuarios concurrentes*

El sistema debe soportar al menos 1.000 sesiones simultáneas en horas punta, garantizando que la búsqueda de viajes no supere los 2 segundos.

*R.N.F. 03. Respaldo de datos periódico*

Como administrador quiero que se realicen copias de seguridad incrementales cada hora y completas cada 24 horas para no perder ventas ni registros de pasos.

*R.N.F. 04. Tiempo real en el control operativo*

Como controlador quiero que los pasos e incidencias registrados se reflejen en el panel de la central en menos de 5 segundos.

*R.N.F. 05. Accesibilidad multiplataforma*

Como cliente quiero poder consultar viajes y comprar billetes desde el ordenador, la tablet o el móvil, en los navegadores más utilizados.

*R.N.F. 06. Seguridad de datos personales*

Como usuario del sistema quiero que mis datos personales y de pago estén cifrados en reposo y en tránsito, cumpliendo el RGPD.

*R.N.F. 07. Auditoría y trazabilidad*

Como administrador quiero que el sistema registre cada cambio en viajes, billetes e incidencias, incluyendo el usuario y la fecha.


---
---


## 5. Modelo conceptual

### 5.1. Diagramas de clases UML

![](https://uml.planttext.com/plantuml/svg/hLVDSjis4BxpAL1TEjDfd3f7JTi9BN4QjOgiaLHVEZsioRKC1mHO0FIXIPwYJz9hhtwnBW3-W19ZipFrHHAmsFtssm-xy5jZGTkw5CbNzXvBP9K0BfFaQ_R32tzaibYd-spFjhlLPh7QfkjiF-LSBi0OzgkfGNF5FYQC5PBFsTvgBe_qIwhoLcEq01KAmGjrERBsfbrZltrtSN7vEstTONuF6yXvoL5QDMTBi4Zh5WNUAQaYBrW25z7lN4chmU3Jtt06g07ShNzEcamMmiL1ducFlSCZDrP7aUdW7Nayy4eT_FV-T5PM0g48HCYwHAtQfGY2zxkIHuscTsj014HBLTyAZ12vD9GW5zgxjRLsQH_Sny2gg7FRs8JGQvwZp7cS_JtSSi4jyQbaau86QIgbFGYFR07aYHTGzESznYvMMfHSEb8wJ0LM93meGudEE4LN2E4jSrNmetfE83clANQqK72DUG3ShPsLlo-12Tdzhz68fy1MwxtZE9K5IZHpjYBL757tGDPS8an4qGSObACYA5njbVwbx7H1joMt_96ASgKKAKls4RT05GaXbIPOFIZ6g6ArLMQ9fi1hpO_3tG_bOEDKWZTup_DQgF5ilW2-13AWKt1-gKjLe8XTv521brmCoeinBVwekU6X8CrLGIg2kr2P_sp1HN0vFE0uLk_pdTAm1seuZR8FlCHuRotmI4HkDJwIoeT69x5TfBW0HWtqzxhpEUgiszqXY7wd9mhaIHbVQtGzJRqFvHoHtHKN0krBwIzKMUaX631DXsrSHkL2gmgr1UVgVVTzl1MseetqEgIimTS3Nb6BANoVyPZvkGOSu07KNd7zOLe-QdX4BwAVcc-3xG9DhdbrShUf2TSexF0-JHgOxx9THgCodJGdsmdv_QTNhzeXCrni1qMII4Nr8mK9BZyGme-vSbiMlgNcP1vgQU5pWh9WpYv9kfaJNFVJudmdJ8YJzK4t_w_19n6rovPicR73BjtijzUxGpRbM3psPfSN5z_C686TNSwwSJ9dz74B3y1GiX-JTjcPU4j_fhkESsRg2ZLTGY3Zq9nRqpVUj6cVS-PIhfrg6VKoCcusPZs4s2iom-tJFmsAuMni0z39477EZkIOJZeGqzYxtcQxTB5w-clZIBnPfJzFUvc5BXY1RX4G2vPcLexCWJWpwvhzdEu6TSa_dLBeBJLcsxyw76lIlnjKnrhK36F_tebpxXzMH38PqkLyvaX4joTvrCvrgzdWjJId_gbSpsaGJUFx9jiSKaVrrMgzpWxJo6wxRinDyv8CUj8zZ17RbXDwHS6uHITEvMVq2qQ54vbKatDsJ_xbz6OMjUoX62BgDRg7W8CJiXuv4ohMvWxKw-dAmNEsaH1KNckDGGzVzjrhcLCRutdmdxob3kR-JVeN)

![](https://uml.planttext.com/plantuml/svg/ZL91ReCm4Bpp2cvzHQKiU4CTOHiP6wczH5QWBHM1Y40lLHxHD_LZNUCeLTDABGSOhsR6ioDMnycDqxplefluze-7A2YjAFz3RVfv7vjvx8VuBOfZ0H_l886G0SsGlbAbTnYTeZEpFGncTFtnC8nJiqZ06fNOqaiqoczCfD2xTkX_uyKnbmQrK0poYyWBwg5gdjlTt8L4ga0DXbUACCgj0599BZXAuqTGeUR0F4nW0zTEhNi9AGkjCWqYCCadjRhqSCqbvFmXp5EGAUOUVzlNZUsZMrmob9GcznQbiOpxC7JOs8JdmOBBDGtvrqgYcOPwADnJM8TgtQ3n1UCQq_DUK1HReIf-NKJITbrphhcYcCiDjWmtWRG_el9-rzODlKBSK0u1ZKR3iY-aAMSOOWiqgBJALSPJpoenivepOBvs4boiiz6zDhMhh-nph1ORnQWKvXseuZ2ozzk0Jz6gwMl_ttu2)

![](https://uml.planttext.com/plantuml/svg/TLTDSnD74BtxLyo5AgW8OvkOa52fGfH58c9BBbdca5jxjn43kpERcLqLph_9CGSE53Uk-cDvFJFx8R6f2gdLVhn-_RfVz_YbhydLJLaS6Lkp8gzEZvIwDIgp9c-osZhbRM5LrN3EwQPsQglf0tk5MyNkis6IoqALsfTMrRgomFYhOUKRzPxkTA5holJkgva8WYf85JfZasbIR82rGHGEzzznzfu2LeW0c7jLiz5iSgi84TXOhyvE5Us-M7zyXFjAY1zrzC_mpGoVy_lTvwm1St1iJ8J3zHr8iJjW6xWAgv1B2CgYYv2I2yHUNizMq_NyxN8ivbDyij0IfbVDXDyzZI2WaMGGdhpU6C1wV8Z_jt86oLW4fqf2uxy21Bcix5ZO7sEgPlUjP1SIXTG5rq1wExsSNonlzf8tRHLzKx4ZXw-HSqOLQfDJ7bNl-BOKZoM84PIerm4MoYjo8eVlOakfEva1nwOUJUCS4Mwanv3w5e-HTeU524cRwzNojzNqQYh8zufCUeeEyrI0l_2y95MHermwI5cdDu2wboHpARsljR6X1Oo205A9J6hHL_wWyV33Eg6JXwi0rtMGTA8B5SF3FFmB0SOoV1OBLJdEj8sCq5twNGoOafNqt-aFrDQzjAKe5Zo6CjcoSlGBW1BAh-dYshe_IlL9FVnONh3FUTdoZEa-LlltOngFv8DJFCHhMIDX7aq0dSmvu9OLKZQEHk2EqyFPpVhsOZvTh6VgxEJ1HBsvVJM_H6tKK_arNxp6e_cbEZzvy2WL9e5sB_8d4Eaj_UmyMZggqm74Xv3Wz6nCs9-0FGLBCXaNy6lRwnDLM-b9tupqPKKU8fEQBcw5oN6STx6lNyyNUEjF8TYrU8lkUOp2ysvSrWw-pVS79k4ry4B8a9hZoZgveJ7yyZ01VJjaK4l3CX22FRVQeu-zgWIlKPYMjODOC8czSpmS634TUO-BWaPduiz0VH96w-_MqRLZeDU45khktGGFzVslVe1lJLDRlzzKKfneCP7jYMEmylOglZe4N37OFbRV1KtT41F6HaWJIWJglTMOeP57x9bg5hx2V3Iyhx-6rA48k8gFztWTHpkIoNV_xX4ud91Ymy5q9PbDQUiDKXXJ_FGaTgRZ3R9siJL33F5OkVidDlLvgxByIcBA7DZ8bg2WGzyfiYiuxglLRBsQtYmdRKD1gDW0Upf8TuG6b0SIV9HftFV3G5sOwTlPQZuDEBgiiBD9qVvMaqP7smuMPVpcOla2i2G2e11RXdacQc-_R1l6VU0DDki84NG4eNZYcMvMKB_Jh8eg3Ohn7GWe81PKwCgx3PJmQKak4sFBU0tbRzyTLUeizAuVkBLNwdQzV7LxCx1-k-fZ7nySbHIXw7UxRtuIbd2FirxD5X5a__tQfLNcCHlNizNLyc9w6O3AEoWYt_eszTvRNSBOyIuikt3Om9LFvorPpgszHdD-6htIxi70O92IQ9_C2SpTDwCpMN3eGizPK-kjLJYzyDyavpSWdBPhskbIEZovF9hqf7F3FQu_bhATSbRzyhrFwMwfiAYKoYGfqnuq0aV1NmGFfIfKpcuSeT3jp9J4ax3QK0QEv1EhzlIfi34smLJjg1oUerYcig_PQPjE3p8nqY5aH48vfbrAsqhD6n-wvgxhZv9g5on1B-0c7ogDLunDd8RdiptlJVvdEeLpIpeao520Zbvc3jq7pSJC9wCyprD0cI3aUVUbJRln3PPtci-9DHJKiCnpy7CGaG5zA6AQVUq9l3rAz4DMtB4l992QRZvVBEOXHov95p5RMyho4xn8wL03z5h1c4A7zzi_DwBT1fgBfo1wjlkQuwyG7vIANJnSf2zn9NyY_GS0)

> Código fuente de los diagramas en [`docs/uml`](docs/uml).

---

### 5.2. Escenarios de prueba

*(Entregable 2)*

---
---

## 6. Matrices de trazabilidad

*(Entregable 2)*

---
---

## 7. Modelo relacional en 3FN

*(Entregable 3)*

### 7.1. Justificación de la estrategia de transformación de jerarquías

*(Entregable 3)*

---
---

### 8. Matriz de trazabilidad MC/SQL (opcional):

*(Entregable 3)*

---
---

## 9. Scripts

### 9.1. [Script de tablas y restricciones](1.scripts_tablas_restricciones.sql)

### 9.2. [Script de funciones y procedimientos](2.scripts_funciones_procedimientos.sql)

### 9.3. [Script de cursores y consultas](3.scripts_cursores_consultas.sql)

### 9.4. [Script de triggers](4.scripts_triggers.sql)

### 9.5. [Script de poblado de tablas](5.script_poblado_tablas.sql)

---
---

## Referencias
