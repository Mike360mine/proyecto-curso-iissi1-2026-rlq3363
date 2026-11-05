# Base de datos para una empresa de transporte.
<!-- Añadir aquí una imagen de portada (logo inventado de TransRuta, foto de una estación, un autobús o un tren...) -->

## Miembros del grupo L5-AM-8
| Integrantes |  |
| :--- | :---: |
| 1. Morilla Rodríguez, Jorge <br><br>2. Gallardo Carvajal, Miguel Ángel <br><br>3. Diaz Aguilar, Miguel <br><br>4. Apellidos, Nombre | <!-- <img src="foto_grupo.jpg" height="300" alt="Grupo"> --> |


---

## Índice
- [Base de datos para una empresa de transporte.](#base-de-datos-para-una-empresa-de-transporte)
  - [Miembros del grupo L5-AM-8](#miembros-del-grupo-l5-am-8)
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
      - [5.2.1 Retraso en la línea Sevilla - Dos Hermanas](#521-retraso-en-la-línea-sevilla---dos-hermanas)
      - [5.2.2 Avería de un tren en Dos Hermanas](#522-avería-de-un-tren-en-dos-hermanas)
- [6. Matrices de trazabilidad](#6-matrices-de-trazabilidad)
  - [Matriz de Trazabilidad - Entidades](#matriz-de-trazabilidad---entidades)
  - [Matriz de Trazabilidad - Relaciones y Restricciones](#matriz-de-trazabilidad---relaciones-y-restricciones)
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

- **Administrador**: Empleado que gestiona la red, la flota, el personal y la planificación de los viajes, y elabora los informes del servicio. Cada línea está gestionada por un administrador.

- **Aforo**: Número máximo de viajeros de un viaje. Coincide con la capacidad del vehículo asignado.

- **Andén**: Zona de una estación donde los vehículos se detienen para que suban y bajen los viajeros. Cada estación tiene un número de andenes y cada paso se asigna a uno de ellos.

- **Anulación**: Paso de un billete VALIDO a ANULADO, por decisión del cliente o por la cancelación del viaje. Un billete anulado no cuenta para el aforo ni para los ingresos.

- **Asiento**: Plaza numerada del vehículo que puede asignarse a un billete. Es opcional.

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

- **Licencia**: Permiso de conducción de un conductor, con un número único y una fecha de caducidad.

- **Línea**: Recorrido fijo, de autobús o de tren, formado por una secuencia ordenada de paradas.

- **Método de pago**: Forma en la que se paga un billete:
  TARJETA, EFECTIVO, APP_MOVIL.

- **Ocupación**: Porcentaje de plazas vendidas de un viaje respecto a la capacidad del vehículo asignado.

- **Overbooking**: Venta de más billetes que plazas tiene el vehículo asignado a un viaje.

- **Parada**: Posición de una estación dentro de una línea, con su orden, los minutos y los kilómetros desde el origen.

- **Paso por estación**: Registro de la llegada de un viaje a una estación de su línea, con la hora prevista, la hora real y el andén.

- **Plaza libre**: Plaza de un viaje que no está ocupada por ningún billete VALIDO. Las plazas libres son la capacidad del vehículo menos los billetes VALIDOS.

- **Puntualidad**: Grado de cumplimiento de los horarios de una línea. Se mide con el retraso medio de sus pasos y el porcentaje de pasos con más de 15 minutos de retraso.

- **Retraso**: Diferencia en minutos entre la hora real y la hora prevista de un paso por estación.

- **Tarifa**: Categoría del cliente que determina el descuento aplicado al precio del billete; la tarifa vigente en el momento de la compra queda guardada en el billete:
  GENERAL, ESTUDIANTE, JUBILADO, INFANTIL.

- **Tipo de estación**: Clasificación de una estación según los medios que la usan:
  AUTOBUS, TREN, INTERMODAL.

- **Tipo de transporte**: Medio con el que opera una línea o un vehículo:
  AUTOBUS, TREN.

- **Tramo**: Parte de una línea entre la parada de origen y la de destino de un billete. Su longitud en km determina el precio.

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

Como cliente quiero consultar los viajes disponibles entre dos estaciones y comprar y anular billetes de forma sencilla para planificar mis desplazamientos.

**2 — Control operativo de la red**

Como controlador quiero registrar el paso de los viajes por las estaciones de mi central y las incidencias que se produzcan para mantener el servicio bajo control.

**3 — Gestión de la red y de los recursos**

Como administrador quiero gestionar las líneas, estaciones, vehículos, empleados y viajes, y obtener informes sobre el servicio, para planificarlo correctamente.

**4 — Consulta de la planificación**

Como conductor quiero consultar los viajes que tengo asignados y el vehículo de cada uno para organizar mi jornada.

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

Como cliente quiero consultar los viajes programados cuya línea pase por una estación de origen y después por una de destino en una fecha determinada, con la hora de paso por el origen, la hora de llegada al destino, el precio del tramo y las plazas libres, ordenados por hora de salida, para elegir el que mejor me convenga.

---

**RF.02 - Historial de billetes del cliente**

Como cliente quiero consultar los billetes que he comprado, con su código, la línea, la fecha del viaje, las paradas de origen y destino, el precio y el estado, pudiendo filtrar por estado y por fechas y viendo el gasto total sin contar los anulados, para llevar un control de mis viajes y gastos.

---

**RF.03 - Agenda del conductor**

Como conductor quiero consultar en orden cronológico los viajes que tengo asignados en un rango de fechas, con la línea, la hora de salida, la hora de llegada prevista, el vehículo y el estado, marcando los cancelados, para organizar mi jornada.

---

**RF.04 - Panel de pasos de la central**

Como controlador quiero listar los pasos previstos para hoy en las estaciones de mi central, con la estación, la línea, el viaje, la hora prevista, la hora real, el andén y el retraso, destacando los de más de 15 minutos y pudiendo filtrar por estación y por línea, para supervisar el servicio en tiempo real.

---

**RF.05 - Incidencias abiertas de la central**

Como controlador quiero listar las incidencias no resueltas de las estaciones de mi central y de los viajes que pasan por ellas, con su tipo, gravedad, fecha, descripción, el controlador que la reportó y el viaje, estación o vehículo afectados, ordenadas de más a menos grave y por fecha, para priorizar su resolución.

---

**RF.06 - Ocupación de los viajes de una línea**

Como administrador quiero consultar, para los viajes no cancelados de una línea en un periodo, los billetes vendidos y el porcentaje de ocupación respecto a la capacidad del vehículo, junto con la ocupación media de la línea, para ajustar la oferta a la demanda.

---

**RF.07 - Puntualidad por línea**

Como administrador quiero obtener, para cada línea en un mes, el número de viajes, el retraso medio de sus pasos registrados y el porcentaje de pasos con más de 15 minutos de retraso, ordenadas de mayor a menor retraso y pudiendo filtrar por tipo de transporte, para detectar las líneas con más problemas de puntualidad.

---

**RF.08 - Ingresos por línea y tarifa**

Como administrador quiero consultar los ingresos de cada línea en un periodo, sin contar los billetes anulados, desglosados por la tarifa aplicada en cada billete, con el top 5 de líneas con más ingresos y pudiendo filtrar por método de pago, para analizar la rentabilidad del servicio.

---

**RF.09 - Vehículos con más averías**

Como administrador quiero listar los vehículos que han tenido incidencias de tipo AVERIA en el último año, ordenados por número de averías, con su tipo, modelo, fecha de adquisición y estado actual e indicando si tienen alguna avería sin resolver, para planificar el mantenimiento y la renovación de la flota.

---

**RF.10 - Conductores con licencia próxima a caducar**

Como administrador quiero listar, ordenados por fecha de caducidad, los conductores cuya licencia caduca en los próximos 60 días, junto con los viajes programados que tienen asignados después de esa fecha, para evitar asignaciones no válidas.


---
---


#### 4.1.1. Requisitos de información

**R.I.01 - Información sobre usuarios y roles**
Como administrador, quiero disponer de la siguiente información sobre los usuarios del sistema. Cada usuario es un cliente o un empleado, y cada empleado es un controlador, un conductor o un administrador:
- **Datos de todos los usuarios:**
    - DNI
    - Nombre
    - Apellido 1
    - Apellido 2 (opcional)
    - Fecha de nacimiento
    - Teléfono
    - Email
    - Contraseña
    - Nombre de usuario
- **Datos de los empleados:**
    - Número de empleado
    - Fecha de ingreso
    - Salario
- **Datos de los administradores:**
    - Departamento
    - Líneas que gestiona

---

**R.I.02 - Información sobre clientes**
Como administrador, quiero disponer de la siguiente información sobre los clientes, además de sus datos como usuarios (R.I.01):
- Fecha de registro
- Tarifa
- Billetes que ha comprado

---

**R.I.03 - Información sobre controladores**
Como administrador, quiero disponer de la siguiente información sobre los controladores, además de sus datos como empleados (R.I.01):
- Turno
- Central en la que trabaja
- Pasos por estación que ha anotado
- Incidencias que ha reportado

---

**R.I.04 - Información sobre conductores**
Como administrador, quiero disponer de la siguiente información sobre los conductores, además de sus datos como empleados (R.I.01):
- Número de licencia
- Habilitación
- Fecha de caducidad de la licencia
- Viajes que conduce

---

**R.I.05 - Información sobre centrales**
Como administrador, quiero disponer de la siguiente información sobre las centrales de control:
- Código
- Nombre
- Ciudad
- Dirección
- Teléfono
- Estaciones que supervisa (al menos una)
- Controladores que trabajan en ella (al menos uno)

---

**R.I.06 - Información sobre estaciones**
Como controlador, quiero disponer de la siguiente información sobre las estaciones:
- Código
- Nombre
- Ciudad
- Dirección
- Tipo
- Número de andenes
- Central que la supervisa
- Paradas de las líneas que se sitúan en ella
- Pasos de los viajes por ella
- Incidencias ocurridas en ella

---

**R.I.07 - Información sobre líneas**
Como administrador, quiero disponer de la siguiente información sobre las líneas:
- Código
- Nombre
- Tipo
- Precio por km
- Activa
- Paradas que la componen (al menos dos)
- Administrador que la gestiona
- Viajes que se realizan en ella

---

**R.I.08 - Información sobre paradas**
Como administrador, quiero disponer de la siguiente información sobre las paradas de cada línea:
- Orden
- Minutos desde el origen
- Km desde el origen
- Línea a la que pertenece
- Estación en la que se sitúa
- Billetes que la tienen como origen
- Billetes que la tienen como destino

---

**R.I.09 - Información sobre vehículos**
Como administrador, quiero disponer de la siguiente información sobre los vehículos de la flota:
- Código
- Tipo
- Matrícula (solo autobuses)
- Modelo
- Capacidad
- Fecha de adquisición
- Estado
- Viajes que realiza
- Incidencias en las que está implicado

---

**R.I.10 - Información sobre viajes**
Como administrador, quiero disponer de la siguiente información sobre los viajes:
- Código
- Fecha y hora de salida
- Fecha y hora de llegada prevista
- Estado
- Línea a la que pertenece
- Vehículo con el que se realiza
- Conductor que lo conduce
- Pasos por las estaciones de su línea
- Billetes vendidos
- Incidencias que le afectan

---

**R.I.11 - Información sobre pasos por estación**
Como controlador, quiero disponer de la siguiente información sobre los pasos de los viajes por las estaciones:
- Fecha y hora prevista (calculada)
- Fecha y hora real (cuando se registra el paso)
- Andén
- Retraso en minutos (calculado)
- Viaje que pasa
- Estación por la que pasa
- Controlador que lo anota (solo cuando se registra la hora real)

---

**R.I.12 - Información sobre billetes**
Como cliente, quiero disponer de la siguiente información sobre los billetes:
- Código
- Fecha y hora de compra
- Asiento (opcional)
- Método de pago
- Tarifa aplicada
- Precio
- Estado
- Cliente que lo compra
- Viaje para el que es válido
- Parada de origen
- Parada de destino

---

**R.I.13 - Información sobre incidencias**
Como controlador, quiero disponer de la siguiente información sobre las incidencias:
- ID
- Fecha y hora
- Tipo
- Gravedad
- Descripción
- Resuelta
- Controlador que la reporta
- Viaje al que afecta (si lo hay)
- Estación en la que ocurre (si la hay)
- Vehículo implicado (si lo hay)

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

Como administrador quiero que el número de billetes VALIDOS de un viaje no pueda superar la capacidad del vehículo asignado, para evitar el overbooking. Además, en un mismo viaje no puede haber dos billetes VALIDOS con el mismo asiento.

Pruebas de aceptación:
- Se intenta vender un billete para un viaje con todas las plazas ocupadas; el sistema rechaza la venta.
- Se intenta vender un billete con un asiento que ya tiene otro billete VALIDO del mismo viaje; el sistema rechaza la venta.
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

Como administrador quiero que, en el momento de la compra, el precio del billete se calcule automáticamente como el precio por km de la línea multiplicado por los km entre origen y destino, aplicando el descuento de la tarifa que tenga el cliente en ese momento: ESTUDIANTE 20 %, JUBILADO 30 % e INFANTIL 50 %. La tarifa aplicada y el precio quedan guardados en el billete. La tarifa JUBILADO solo puede tenerla un cliente con al menos 65 años y la INFANTIL uno con menos de 12.

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

**RN.12- Validez de los datos de los usuarios**

Como administrador quiero que el DNI, el email y el nombre de usuario sean únicos, que la contraseña tenga al menos 8 caracteres y que la fecha de registro de un cliente no sea posterior a la fecha actual, para evitar usuarios duplicados y datos erróneos.

Pruebas de aceptación:
- Se intenta registrar un usuario con un DNI que ya existe; el sistema rechaza el registro.
- Se intenta registrar un usuario con una contraseña de 6 caracteres; el sistema rechaza el registro.
- Se intenta dar de alta un cliente con fecha de registro posterior a hoy; el sistema rechaza el alta.
- Se registra un usuario con datos únicos y contraseña válida; el sistema lo acepta.

---

**RN.13- Validez de los datos de la red y de la flota**

Como administrador quiero que los códigos de centrales, estaciones, líneas, vehículos, viajes y billetes, el número de empleado, el número de licencia y la matrícula sean únicos; que los autobuses tengan matrícula; y que la capacidad de los vehículos, el número de andenes de las estaciones y el precio por km de las líneas sean mayores que 0.

Pruebas de aceptación:
- Se intenta dar de alta una estación con un código que ya existe; el sistema rechaza el alta.
- Se intenta dar de alta un autobús sin matrícula; el sistema rechaza el alta.
- Se da de alta un tren sin matrícula; el sistema lo acepta.
- Se intenta dar de alta un vehículo con capacidad 0; el sistema rechaza el alta.

---

**RN.14- Estructura de las líneas y horario de los viajes**

Como administrador quiero que el orden de las paradas de una línea sea único y consecutivo empezando en 1, que la primera parada tenga 0 minutos y 0 km desde el origen y que ambos valores crezcan con el orden, que una estación no aparezca dos veces en la misma línea, que no se puedan programar viajes en líneas inactivas y que la llegada prevista de un viaje sea posterior a su salida.

Pruebas de aceptación:
- Se intenta añadir una parada con orden 4 a una línea que tiene dos; el sistema rechaza la parada.
- Se intenta añadir una segunda parada con menos km desde el origen que la primera; el sistema la rechaza.
- Se intenta añadir a una línea una estación que ya es parada de ella; el sistema la rechaza.
- Se intenta programar un viaje en una línea inactiva; el sistema lo rechaza.
- Se intenta programar un viaje con la llegada prevista anterior a la salida; el sistema lo rechaza.

---

**RN.15- Registro de los pasos por estación**

Como controlador quiero que la hora prevista de un paso se calcule como la salida del viaje más los minutos desde el origen de la parada, y el retraso como la diferencia en minutos entre la hora real y la prevista. La estación del paso debe ser una parada de la línea del viaje, el andén no puede superar el número de andenes de la estación y el paso solo tiene controlador cuando se registra su hora real.

Pruebas de aceptación:
- Un viaje sale a las 08:00 y la parada está a 20 minutos del origen; la hora prevista del paso es las 08:20.
- Se registra la hora real de ese paso a las 08:25; el retraso es de 5 minutos y el paso queda asociado al controlador que lo anota.
- Se intenta registrar un paso en el andén 7 de una estación con 4 andenes; el sistema lo rechaza.
- Se intenta registrar un paso en una estación que no es parada de la línea del viaje; el sistema lo rechaza.

---

**RN.16- Gestión de las incidencias**

Como controlador quiero que toda incidencia esté asociada al menos a un viaje o a una estación, y que una incidencia resuelta no pueda volver a marcarse como no resuelta.

Pruebas de aceptación:
- Se intenta registrar una incidencia sin viaje ni estación; el sistema la rechaza.
- Se intenta marcar como no resuelta una incidencia resuelta; el sistema lo rechaza.
- Se registra una incidencia asociada a una estación; el sistema la acepta.


---
---

### 4.2. Mapa de historias de usuario

**1 — CONSULTA Y COMPRA DE BILLETES** (Cliente)
* RF.01 - Búsqueda de viajes disponibles
  - Buscar viajes por estación de origen, destino y fecha
  - Ver la hora de salida y de llegada de cada viaje
  - Consultar el precio del tramo y las plazas libres
* RF.02 - Historial de billetes del cliente
  - Consultar los billetes comprados
  - Filtrar por estado y por fechas
  - Ver el gasto total del periodo

**2 — CONTROL OPERATIVO DE LA RED** (Controlador)
* RF.04 - Panel de pasos de la central
  - Ver los pasos previstos del día en las estaciones de la central
  - Consultar la hora real, el andén y el retraso de cada paso
  - Detectar los pasos con más de 15 minutos de retraso
* RF.05 - Incidencias abiertas de la central
  - Listar las incidencias no resueltas
  - Ordenarlas por gravedad y fecha
  - Filtrar por tipo de incidencia

**3 — GESTIÓN DE LA RED Y DE LOS RECURSOS** (Administrador)
* RF.06 - Ocupación de los viajes de una línea
  - Consultar los billetes vendidos de cada viaje
  - Calcular la ocupación de cada viaje y la media de la línea
* RF.07 - Puntualidad por línea
  - Calcular el retraso medio de cada línea
  - Ver el porcentaje de pasos con más de 15 minutos de retraso
  - Filtrar por tipo de transporte
* RF.08 - Ingresos por línea y tarifa
  - Calcular los ingresos de cada línea
  - Desglosar los ingresos por tarifa
  - Ver el top 5 de líneas y filtrar por método de pago
* RF.09 - Vehículos con más averías
  - Listar los vehículos con averías en el último año
  - Ver su estado y si tienen averías sin resolver
* RF.10 - Conductores con licencia próxima a caducar
  - Listar los conductores cuya licencia caduca en 60 días
  - Ver los viajes que tienen asignados después de la caducidad

**4 — CONSULTA DE LA PLANIFICACIÓN** (Conductor)
* RF.03 - Agenda del conductor
  - Consultar los viajes asignados en un rango de fechas
  - Ver la línea, el horario y el vehículo de cada viaje
  - Identificar los viajes cancelados


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


![](https://uml.planttext.com/plantuml/svg/VLTDSnD74BtxLymbLL0HnZ8n8Q6eGfH5eiH8BbdcaDjuTn03kpERcLqLoh_9CGSE53Uk-cDvtJEp7_8cLO3NkpFThrztlsvU-bgwkYcB4sDh9QGNqnCXRet8hCcRhBPEU5jOKJKgL_6bTcAdvGVb1LuLXyz6INeiHAbzQKMjAmiRVpPA-4QybtUwqBNCzE6hcP05KKXHw4oPJ4kX36njuKNn-tSgUo_P5dk0cRsebT7Av5P8U536Ud4-5VBmnVhJ4xmN1FoaXN-EEtDSL-yFdxC6o86nCS4Sdky0IhaZj8oLK74ix5GHBmIA7k1xTJrVppQBjwindqzmPQd9JSUQuTyx64oWeM3WNdgzDJ3hSH7_9ZgPCaM4oufSuqy1WAgix9ZR7qAevl2jL8u31TM5gc7fxUngSRcw6GHlKXPzKoad7MuZvanMo4qkyy1wYpT1F2KdXgm4levi8Rr49sAuvvjItT8CSyhKes5SmCCDrHX2t-4pmcvjmKFavdgz-cKzUpCZotiXJVoA2lEo07x2UIL59PtCgOA4TNeBKtiACgVK-reRooLW10YWJ6IKYoxpHuM7NwmZE3a_iRcsWgWI7MSC7tF-omx68dmQ4bKvbMaR4A6wzBlWC0PBuR_J7sJAUsbBOenx36coPUNapp0KhRo83zVM_LwAJ-B1n_9I-Loj4iuGxYCnV1_2U4WNflY8Orb3RXvEO3esPmzRLb3QL6W4rM9wCB_Pt5ukPilDN9oVVJSHlzs-MbmXD-89_RPOliQdnPMuEFlkOKnCDDeUL9y0f6lffnUXfGCxhO7m4HHCpyU8_H6sPq0fJQOAz6kgzOceBTMaRqRgif8U94inMzuIajEWTw6kNo-MEFK70MnBF5dtQWp2irOkQuU-pOU2AN4CkE2IGtEgievUQ8XVpWheayWWbqQH871xxhH77NjHaRr6G2rh1xBG9FREgRvWX73ed2eAEQg9FmMQdDfg3pFC4q7YX9U7lmF35sXLqzI1RwTmq7DFRaco9Ex87cmIBXN4SptVh6StgqcA3kqNq0n88QZC1dqWvsD0fsVCPjzHwsRsThvUpDYEBYiC42daK6A9TN3OK-rmvtBr76Ob4O3Kx1GoEH43iTir2kzXxtzbVZhbKlQzT7NeRZUhLxStlTmdhG-AT3GhXGJHTuTlVi8gtDdPhEVBO6HuldPHopoQOpDVlrbTpgxOK7c7ZD7TqTHFupoyN2uuPbLANGHjCxQaKg8HuHjq9zMM4OTlHcTaYxkMCoYzEdoHSPQainXBXXO2Q7i9c1e_dw5LdCng1PxZ_JJFK-sbSPnqfPj9n9JrkAjjr6HOQcGn6XGDt2k4dHs-vjWUcA7O8ifFUlcP90udNRRfERJFlXK7IeCQZ58CtKAXXYP7xzlDXOMgbBLZg_8UUK35IMuybybTMn3TdUSeMS-fnH5Z4-UzYJmey6CeacObXw7QuKATzx5aCANSMnT5ucoKnhX0f277rTKhL-huE1XX9iG6PwW9hxAcrZjBBAc_92rdi31DCzZfaXGZpk9GAMUjkkpn38WvJINHJTPzBEMTB1XsXe2KILi4OpoDwX3oYtsognzmvdYSypg59nytIbe_KXXqVycQ5ZkqSdRhP2dRXPRknARJ1WMeT_9UAQDYjYnoJa6bQpbOifG_BcCgxhYXZAI0bf_7RFfNw-HrClb2jEzk-7AtceZl4vF3UK--0kC4-74Ky3UHz9x1jO8KFXBtdCOuEt93N3meshM7QgBRiKOsfpXE-LRIYDverCWponkUmkS1hjDGaQYempy30CVBwt-qGi3KXZ1A-TEn6OFm993GR6vt4jcbrOem07kMxnb1qMAC87ajrXrF6q4ouJ9Q889UudiwsmFu4a_q_x1_0G00)


---

### 5.2. Escenarios de prueba
#### 5.2.1 Retraso en la línea Sevilla - Dos Hermanas
![](https://uml.planttext.com/plantuml/svg/ZLVDRkCs4BxxARZfeK01svGi9tQ0BQAr1Qnt_GVRyNN1I4p2L19LIWhG57s47leOFUoXwArNlrXdIDaHPHj9JixCSFZDnvcFr4rUC5MKITpwK3poX9CiPY9jjNua7z_uWv35fo_1Ph4cus1AHlvwSKrMmMO5FqWm9rEVJ7T_pWEVh8FjP0h_jiaOWZy7gva_zzVlsQ8bxvvuM92Ba3hacenuMYWMNn2M4xJytY8ab95uaEGZkHXHvm8CgKpk5DS64qxM_5d4CKDdACg8HUYi6IEXU1WAcQBTV-wGY9ElBCrXbv4eVYEEYr45Z_czJFLMmxv7ANKSsF2FLv0iBMGg90ALiBECMIIL1hlt8E0e5PYYHnstv_KlfpPctqIYaMKyZaKa7RIlIl5IDxfed36r-vwY-Pw7ZspEGf48A1enKhVhE5rdU3LeOBzq7DTrUxqUhkC94p5QAuGT1VlS06jfhig2TNYkMSDoMCvt_p3DQw7Y7rpgNh9NxBTvoPJG6p1LOKtBX2iP95dCWGbq1RDbcphqW7YIFYYUeylfTm6n2wI0BsTnbSnrAErGhA1K6lxCt_q5tLEdFIv3mO1qFqf4Ad90kgVTU6gakuEhoxxNSx_Qf4_BSFUT7NCk4VyHwrl-m4vpxhXTsWFE1xJ9UTzpDEqMvnfUH-bTtk8SYdFI3qZwISvZTS1wXdKp70tMgTEbGsIzRxC-g5YFU0Rgm9Aglejbp59nBq8eUlTlQZK_wCOByrK2j5-J05G51qgVGirZ3spG7DWbHkBqwUZtpUko170S5iLanC97gPGaULSFHI4otCU_tIm-tQuFrFXfn5EUW-UoNa0ayyySzasFAwYxcYMuTWbZcPDzh5r0qtDATG0QYtKp4e_MIfZCDw2QYx4_FL65Pms35igfI3ap8t0aazCcwnMpACyDY4S4PW1PoALKNnFidew3JSB2GZnZanQgv5PB89GbWtaqM383HIf0ZjC2ls5YoqBcOvv7VA74WtPWqb-IXWtwiPxQRQHshTJkkTJkgTnEtquEzPVGJbh6ep8iAZMfp7Kv6OA0Nms6CvlGcGuyafE5AYhbiEHa3AVzSbfFNDgbbwWd_QQUQ07tFC_I4mEm8_K-RueurFOJwaboKb0IUK1xHb3SKp9E1rtG512KWIKeph1l1CNacegGfoZDs8wJKTlphdHFFh8x4GipU5RVwLr630v3m3JLblT0mFgmdpki7-1TcUinsF974PQnrCTdZFOiGFQs6OUZLazOeN2n1ebNTNiwcsDa8a5hzEg94YzC4Q4bEMGPq-Z0q-_lCVlHhwN8HLKGogs7QB4_EIYCfdEn35R-Ph9Tr4juVi871jOWs1FN1LGckuBjbpQjQ_jdgTYQGNCXReVY6m0F6Gxccj9wq3I6uutOKi7q0vAZQ3qf1up1_DleThMs86RiBjQFdHZxrM0qjjg8N5qD1iCXfPk6qcFSqOHy4SdkxvGVFtH63AvrUURIzSpBWNhD8HaEySvjF7GqmiwJW3kDlzo4S5zmUNf2DCOE9Zuv8LcLuu1gnH_mzI4DbVfUTVNWmefxp1YiDxVZYGzQRZKhfTLHVm8PvXMHUskZNzl6R1ttI2QPmlmm2Vi3T0RNFQtD-UlR17kN5pAIIwQp-Slbjza2dkPdCExb7XTtx7RO-jF9kD4CkJHodqlxzjSEBADxG7oswUmMNN4M7uTW7ImrEj_3j8hZgScPqNkj_WrWxZbWxdk1kMy3ysWDc7CQc7NDY3Gqvpu1COkqecbWbHsHYQZATTZwzBQLUbLVNs1uKEoP6p6Q1jj0F-NoK8bi_-1O6Mpuw73f_guaWBt-1Y2PLAHC2IjO7ahqWXpwRJqF8FXmsMYgxbcSwul_NH-Dgs3gZoQBUR06LTciWlrNvBk-1_SVKkqsCP-4KCKTUsA8xUSMci1ZFKNpCkCA3fMz-ksNNZt0V310Y7b6FK330r_QKwL6SmW3L-8iQ7vUVUu9NtO24IUvA7R_LQ1XsJ4cEwHI_WCk05o0z8ISCCkxl_SKz2TSJJ2g86bMnE5f4-gd3UXhXNElDmYsqlLGwygfYCFb15y7hB4OpTAyh6m7iXCXeQ3EKBMMXNekPUIJwS74lZH1xdk2ZaasrExNs-mUV7hPwrSxIsNnMkzXyRc0Qawj2CN11p7iAEPG0eDPXlPWbjS6ACCIhYM3itN3uQIIkFK_)

#### 5.2.2 Avería de un tren en Dos Hermanas
![](https://uml.planttext.com/plantuml/svg/dLZDSjis4BxhAH3tq9Caa1BruvbqJCjA8aUMDVBFJ8ym2TjmGK85IS_KdJv23vq-GHyWXqvklUh5kWkG4a79ZQUv9De55zz-m7wxo4cMCvqNYMozonzvmibQCf6sMj-Jzz_u0qikJy-drvTNv6mw9vFmwlAOXBVJrURt42paPa6kLzC5-H5SYybq7avccpyNPktjB3oVlcMBbhfxub5EZYBgaMCouMckcJmYB2De-RL5IAHYyQ38Ut8qeTuH65ALt6bk37OvkUBFGag6paWKCOlHMJF6GlCe4Yf5U_ZS8J4ddrcQmIuJaVz2F1zNvLpoUvMQhSP1drBgUR3XRpkGBCrLAXG2LR2paYnMse2jF0WuJWM6w57Fx_M3mTp57DgLQ6HhBgM8bOVsLI5UwaOVZHTCRxwaQBxdqIDRi4Wa0f96ZDJlUbxN6mz73Um3p_DzlzVhuNSyOKAYjKJOqR3F2R2MPhh8MOTdXZLCXsLyypSplEPQVkTJVy1ss6-oWcbXDc2wn9eM2TTgcgmb1oRGDRrOjgb7juXdwOFc6Rgye0k8VI05V1cJPJ3VexH3COD26_WNuUQFS17MQPT591YG7iQ9I4K6I2lQhQT6kZyQ3e9-p__iaZuleiqNji-vGlnxhD_o1tQOSy_lqXvmFg9DpeE-PsXtE3VmEjhiyYtE8JalVOUa7-HSwYtMLrYtnT5WdNfTEaRM0vVrKSbwpDUW3YmfypjQIfQAUn51qfklgNFvCxX1xBm0EO6ZcC9VM53c56eUjs2djE-UGrcJv0g_8EQJ_rsqMTSKHYxMk1SgqPQRC8rvoZDpOFKKOfLzuZfXASHev51tDPFmtIJEL4QgjIxwfkTG2e2DINCTIP_M4fWjhgUhYykpS7uWZNuzYuZ5ugM9tngRo0SDlTpy5TlAtM7UsHoqS15U6EAjZknDT0TEsL8VKCv5ofajLlZj0fmtgQqsQvCTB7V7DM0KQgdqvmIlTuUY-h4e5yzOHRakU1t2kWy8bWm4mqA0tuX0QG2CjmF-3P9Iv2exurdCBxLuC0uiZP-IXWqAfXuwQ8GEdD3-Qw5D9JPZU_r6y44Z-C09tdirE3s4tFC3DxnMKTN5uYBAIp6rrde7yuB1S3IcI_UGLWfkxtu3Mx1dfW-ewKSXNsbW0MgJDnxlYMclr-_tWo1mnDJ0wwHsbs-fwRNcLaprGJNLwGxiAtBg7v9JQBYW_o2dOrTEQM3brCQQYuYds9ZmYiycxM3acqf_P7T22bkqknjjjfWmE0W19LdxjWURUR1Pppcx73AZFJYyM_ueea8gStQbrIqltA1DU_j5bAYOIpjIXHz0JP-5bM6sPWO4Rcvkai4MnZyN8XCbQkpZWue23X9aECEnS1QUNTQHFZ_123L4f88zSGEpDBamRy_RQDtI_KbfTiNW_Z3RSJqamH_0vJxUWS95SmcDEcPB3Qg1pSTTxHttQHrYEQIw62EceVTXAKYybhpKKcEiZnL3l991i6hegLcuLmdppTSrtn_cbbhmN1skXHxjqY7KGX3i3HR1U3mQ3OTEBLY47QbWdvD8QStLAvESMOXn3zP1P29i8Qtu0uvNzafteJw1oJu2WlNt6E_yvdJMfF2EqlASJq6xUSbXPNMF-hHjpSvPJrIorXYTZdQdvzkpPbd5K1-PIO30M2sPYNOThk1r4Xx4LpKAi0qxrBa7uUAccSAQPSfsYamvNTKwC8Vk5kuhrwrvELUSoVqbs75OQbKSErTNSpmkTM7LtFIa6Z2HHjHqqHbeGco4mU0gxGXCn6MtsUwyjwiPkKi5i8KAlnyqU-QsrZ-km6g6mIpIObsD01-O1940g5m24Mc--GhVt2jO9etU44QWLPidM6n6XURu0w9PS6cOkcSoCvtvJS_EbNbCNYwcLsIw0Csgtg5lUb5MJx5sczX79U2xOq-8bFpGGXDud64sAzPSmvcodT-T50yig8Qmkik-Hi1f9vy7k790fV600Unc78dWkXTu6ybQwPel-0_Vu80FWkqbmaacyiq_PQuGSZ-LnfA1auphBDLwcn1aciEW5t727E-s9Mcuy-85omnao5bIJHoHcJX04SiaAfam84efZemU75goxIZmQ66kor2hxCJZEf2x68y211KIDXLTZwMjw35Jx7YDQuiSTYrXbMltVm4iLVaERbcQpWhDmPTZbJJNR54mA5_OXJbUz-XKL83GMnYxDLlo1Kmmy70p8Led77XEPEjV)

---
---

## 6. Matrices de trazabilidad

- Matriz de trazabilidad entre los elementos del modelo conceptual y los requisitos.

# Matriz de Trazabilidad - Entidades

| Requisito | Usuario | Cliente | Empleado | Controlador | Conductor | Administrador | Central | Estacion | Linea | Parada | Vehiculo | Viaje | PasoEstacion | Billete | Incidencia |
|:---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **RI.01 - Información sobre usuarios y roles** | **X** | | X | | | X | | | X | | | | | | |
| **RI.02 - Información sobre clientes** | X | **X** | | | | | | | | | | | | X | |
| **RI.03 - Información sobre controladores** | | | | **X** | | | X | | | | | | X | | X |
| **RI.04 - Información sobre conductores** | | | | | **X** | | | | | | | X | | | |
| **RI.05 - Información sobre centrales** | | | | X | | | **X** | X | | | | | | | |
| **RI.06 - Información sobre estaciones** | | | | | | | X | **X** | | X | | | X | | X |
| **RI.07 - Información sobre líneas** | | | | | | X | | | **X** | X | | X | | | |
| **RI.08 - Información sobre paradas** | | | | | | | | X | X | **X** | | | | X | |
| **RI.09 - Información sobre vehículos** | | | | | | | | | | | **X** | X | | | X |
| **RI.10 - Información sobre viajes** | | | | | X | | | | X | | X | **X** | X | X | X |
| **RI.11 - Información sobre pasos por estación** | | | | X | | | | X | | X | | X | **X** | | |
| **RI.12 - Información sobre billetes** | | X | | | | | | | | X | | X | | **X** | |
| **RI.13 - Información sobre incidencias** | | | | X | | | | X | | | X | X | | | **X** |
| **RN.01 - Habilitación del conductor** | X | | | | X | | | | X | | | X | | | |
| **RN.02 - Compatibilidad del vehículo con la línea** | | | | | | | | | X | | X | X | | | |
| **RN.03 - Prohibición de solapamientos** | | | | | X | | | | | | X | X | | | |
| **RN.04 - Control de aforo** | | | | | | | | | | | X | X | | X | |
| **RN.05 - Condiciones de venta de billetes** | | | | | | | | | X | X | | X | | X | |
| **RN.06 - Cálculo del precio del billete** | X | X | | | | | | | X | X | | | | X | |
| **RN.07 - Anulación automática por cancelación** | | | | | | | | | | | | X | | X | |
| **RN.08 - Ámbito de actuación del controlador** | | | | X | | | X | X | | | | X | X | | X |
| **RN.09 - Incidencia automática por retraso** | | | | X | | | | X | | | | X | X | | X |
| **RN.10 - Bloqueo de vehículos averiados** | | | | | | | | | | | X | | | | X |
| **RN.11 - Compatibilidad de estaciones y líneas** | | | | | | | | X | X | X | | | | | |
| **RN.12 - Validez de los datos de los usuarios** | X | X | | | | | | | | | | | | | |
| **RN.13 - Validez de los datos de la red y de la flota** | | | X | | X | | X | X | X | | X | X | | X | |
| **RN.14 - Estructura de las líneas y horario de los viajes** | | | | | | | | X | X | X | | X | | | |
| **RN.15 - Registro de los pasos por estación** | | | | X | | | | X | | X | | X | X | | |
| **RN.16 - Gestión de las incidencias** | | | | | | | | X | | | | X | | | X |
| **RF.01 - Búsqueda de viajes disponibles** | | | | | | | | X | X | X | X | X | | X | |
| **RF.02 - Historial de billetes del cliente** | | X | | | | | | | X | X | | X | | X | |
| **RF.03 - Agenda del conductor** | | | | | X | | | | X | | X | X | | | |
| **RF.04 - Panel de pasos de la central** | | | | X | | | X | X | X | | | X | X | | |
| **RF.05 - Incidencias abiertas de la central** | | | | X | | | X | X | | | X | X | X | | X |
| **RF.06 - Ocupación de los viajes de una línea** | | | | | | | | | X | | X | X | | X | |
| **RF.07 - Puntualidad por línea** | | | | | | | | | X | | | X | X | | |
| **RF.08 - Ingresos por línea y tarifa** | | | | | | | | | X | | | X | | X | |
| **RF.09 - Vehículos con más averías** | | | | | | | | | | | X | | | | X |
| **RF.10 - Conductores con licencia próxima a caducar** | X | | X | | X | | | | | | | X | | | |

# Matriz de Trazabilidad - Relaciones y Restricciones

| Requisito | Controlador → Central | Central → Estacion | Linea → Parada | Parada → Estacion | Administrador → Linea | Viaje → Linea | Viaje → Vehiculo | Viaje → Conductor | PasoEstacion → Viaje | PasoEstacion → Estacion | PasoEstacion → Controlador | Billete → Cliente | Billete → Viaje | Billete → Parada (O) | Billete → Parada (D) | Incidencia → Controlador | Incidencia → Viaje | Incidencia → Estacion | Incidencia → Vehiculo | Card_Linea_Parada | Card_Central_Estacion | Card_Central_Controlador | Card_Paso_Controlador | Card_Incidencia_Viaje_Estacion | Herencia_Usuario | Herencia_Empleado |
|:---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **RI.01** | | | | | X | | | | | | | | | | | | | | | | | | | | X | X |
| **RI.02** | | | | | | | | | | | | X | | | | | | | | | | | | | X | |
| **RI.03** | X | | | | | | | | | | X | | | | | X | | | | | | X | | | | X |
| **RI.04** | | | | | | | | X | | | | | | | | | | | | | | | | | | X |
| **RI.05** | X | X | | | | | | | | | | | | | | | | | | | X | X | | | | |
| **RI.06** | | X | | X | | | | | | X | | | | | | | | X | | | X | | | | | |
| **RI.07** | | | X | | X | X | | | | | | | | | | | | | | X | | | | | | |
| **RI.08** | | | X | X | | | | | | | | | | X | X | | | | | X | | | | | | |
| **RI.09** | | | | | | | X | | | | | | | | | | | | X | | | | | | | |
| **RI.10** | | | | | | X | X | X | X | | | | X | | | | X | | | | | | | | | |
| **RI.11** | | | | | | | | | X | X | X | | | | | | | | | | | | X | | | |
| **RI.12** | | | | | | | | | | | | X | X | X | X | | | | | | | | | | | |
| **RI.13** | | | | | | | | | | | | | | | | X | X | X | X | | | | | X | | |
| **RN.01** | | | | | | X | | X | | | | | | | | | | | | | | | | | X | |
| **RN.02** | | | | | | X | X | | | | | | | | | | | | | | | | | | | |
| **RN.03** | | | | | | | X | X | | | | | | | | | | | | | | | | | | |
| **RN.04** | | | | | | | X | | | | | | X | | | | | | | | | | | | | |
| **RN.05** | | | X | | | X | | | | | | | X | X | X | | | | | | | | | | | |
| **RN.06** | | | | | | | | | | | | X | | X | X | | | | | | | | | | X | |
| **RN.07** | | | | | | | | | | | | | X | | | | | | | | | | | | | |
| **RN.08** | X | X | | | | | | | | X | X | | | | | X | | X | | | X | X | | | | |
| **RN.09** | | | | | | | | | X | X | X | | | | | X | X | X | | | | | | | | |
| **RN.10** | | | | | | | X | | | | | | | | | | | | X | | | | | | | |
| **RN.11** | | | X | X | | | | | | | | | | | | | | | | | | | | | | |
| **RN.12** | | | | | | | | | | | | | | | | | | | | | | | | | X | |
| **RN.13** | | | | | | | | | | | | | | | | | | | | | | | | | | X |
| **RN.14** | | | X | X | | X | | | | | | | | | | | | | | X | | | | | | |
| **RN.15** | | | X | | | X | | | X | X | X | | | | | | | | | | | | X | | | |
| **RN.16** | | | | | | | | | | | | | | | | | X | X | | | | | | X | | |
| **RF.01** | | | X | X | | X | X | | | | | | X | | | | | | | X | | | | | | |
| **RF.02** | | | | | | X | | | | | | X | X | X | X | | | | | | | | | | | |
| **RF.03** | | | | | | X | X | X | | | | | | | | | | | | | | | | | | |
| **RF.04** | X | X | | | | X | | | X | X | | | | | | | | | | | X | X | X | | | |
| **RF.05** | X | X | | | | | | | X | X | | | | | | X | X | X | X | | | X | | X | | |
| **RF.06** | | | | | | X | X | | | | | | X | | | | | | | | | | | | | |
| **RF.07** | | | | | | X | | | X | | | | | | | | | | | | | | | | | |
| **RF.08** | | | | | | X | | | | | | | X | | | | | | | | | | | | | |
| **RF.09** | | | | | | | | | | | | | | | | | | | X | | | | | | | |
| **RF.10** | | | | | | | | X | | | | | | | | | | | | | | | | | X | X |


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
