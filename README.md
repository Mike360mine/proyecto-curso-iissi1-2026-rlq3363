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
      - [5.2.1 Configuración de la red](#521-configuración-de-la-red)
      - [5.2.2 Estación de tren en una línea de autobús](#522-estación-de-tren-en-una-línea-de-autobús)
      - [5.2.3 Planificación de viajes](#523-planificación-de-viajes)
      - [5.2.4 Conductor sin la habilitación adecuada](#524-conductor-sin-la-habilitación-adecuada)
      - [5.2.5 Vehículo asignado a dos viajes solapados](#525-vehículo-asignado-a-dos-viajes-solapados)
      - [5.2.6 Compra de billetes con distintas tarifas](#526-compra-de-billetes-con-distintas-tarifas)
      - [5.2.7 Venta por encima del aforo](#527-venta-por-encima-del-aforo)
      - [5.2.8 Control de pasos e incidencia por retraso](#528-control-de-pasos-e-incidencia-por-retraso)
      - [5.2.9 Controlador fuera de su central](#529-controlador-fuera-de-su-central)
      - [5.2.10 Avería de un vehículo y cancelación del viaje](#5210-avería-de-un-vehículo-y-cancelación-del-viaje)
- [6. Matrices de trazabilidad](#6-matrices-de-trazabilidad)
  - [Matriz de Trazabilidad - Entidades](#matriz-de-trazabilidad---entidades)
  - [Matriz de Trazabilidad - Relaciones](#matriz-de-trazabilidad---relaciones)
  - [Matriz de Trazabilidad - Restricciones](#matriz-de-trazabilidad---restricciones)
- [7. Modelo relacional en 3FN](#7-modelo-relacional-en-3fn)
  - [7.1. Justificación de la estrategia de transformación de jerarquías](#71-justificación-de-la-estrategia-de-transformación-de-jerarquías)
- [8. Matriz de trazabilidad MC/SQL](#8-matriz-de-trazabilidad-mcsql-opcional)
- [9. Scripts](#9-scripts)
  - [9.1. Script de tablas y restricciones](1.scripts_tablas_restricciones.sql)
  - [9.2. Script de funciones y procedimientos](2.scripts_funciones_procedimientos.sql)
  - [9.3. Script de cursores y consultas](3.scripts_cursores_consultas.sql)
  - [9.4. Script de triggers](4.scripts_triggers.sql)
  - [9.5. Script de poblado de tablas](5.script_poblado_tablas.sql)
- [Referencias](#referencias)

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

El modelo conceptual tiene 15 clases y dos jerarquías completas y disjuntas: **Usuario** se especializa en Cliente y Empleado, y **Empleado** en Controlador, Conductor y Administrador. Se divide en tres diagramas: las clases con sus asociaciones, los tipos enumerados y las restricciones que no se pueden expresar con multiplicidades (R.01 a R.16), cada una con la regla de negocio de la que procede.

**Diagrama de clases**

![Diagrama de clases](https://uml.planttext.com/plantuml/svg/hLVDSjis4BxpAL1TEjDfd3f7JTi9BN4QjOgiaLHVEZsioRKC1mHO0FIXIPwYJz9hhtwnBW3-W19ZipFrHHAmsFtssm-xy5jZGTkw5CbNzXvBP9K0BfFaQ_R32tzaibYd-spFjhlLPh7QfkjiF-LSBi0OzgkfGNF5FYQC5PBFsTvgBe_qIwhoLcEq01KAmGjrERBsfbrZltrtSN7vEstTONuF6yXvoL5QDMTBi4Zh5WNUAQaYBrW25z7lN4chmU3Jtt06g07ShNzEcamMmiL1ducFlSCZDrP7aUdW7Nayy4eT_FV-T5PM0g48HCYwHAtQfGY2zxkIHuscTsj014HBLTyAZ12vD9GW5zgxjRLsQH_Sny2gg7FRs8JGQvwZp7cS_JtSSi4jyQbaau86QIgbFGYFR07aYHTGzESznYvMMfHSEb8wJ0LM93meGudEE4LN2E4jSrNmetfE83clANQqK72DUG3ShPsLlo-12Tdzhz68fy1MwxtZE9K5IZHpjYBL757tGDPS8an4qGSObACYA5njbVwbx7H1joMt_96ASgKKAKls4RT05GaXbIPOFIZ6g6ArLMQ9fi1hpO_3tG_bOEDKWZTup_DQgF5ilW2-13AWKt1-gKjLe8XTv521brmCoeinBVwekU6X8CrLGIg2kr2P_sp1HN0vFE0uLk_pdTAm1seuZR8FlCHuRotmI4HkDJwIoeT69x5TfBW0HWtqzxhpEUgiszqXY7wd9mhaIHbVQtGzJRqFvHoHtHKN0krBwIzKMUaX631DXsrSHkL2gmgr1UVgVVTzl1MseetqEgIimTS3Nb6BANoVyPZvkGOSu07KNd7zOLe-QdX4BwAVcc-3xG9DhdbrShUf2TSexF0-JHgOxx9THgCodJGdsmdv_QTNhzeXCrni1qMII4Nr8mK9BZyGme-vSbiMlgNcP1vgQU5pWh9WpYv9kfaJNFVJudmdJ8YJzK4t_w_19n6rovPicR73BjtijzUxGpRbM3psPfSN5z_C686TNSwwSJ9dz74B3y1GiX-JTjcPU4j_fhkESsRg2ZLTGY3Zq9nRqpVUj6cVS-PIhfrg6VKoCcusPZs4s2iom-tJFmsAuMni0z39477EZkIOJZeGqzYxtcQxTB5w-clZIBnPfJzFUvc5BXY1RX4G2vPcLexCWJWpwvhzdEu6TSa_dLBeBJLcsxyw76lIlnjKnrhK36F_tebpxXzMH38PqkLyvaX4joTvrCvrgzdWjJId_gbSpsaGJUFx9jiSKaVrrMgzpWxJo6wxRinDyv8CUj8zZ17RbXDwHS6uHITEvMVq2qQ54vbKatDsJ_xbz6OMjUoX62BgDRg7W8CJiXuv4ohMvWxKw-dAmNEsaH1KNckDGGzVzjrhcLCRutdmdxob3kR-JVeN)

*Código: [diagrama_clases.puml](docs/uml/diagrama_clases.puml)*

**Tipos enumerados**

![Enumerados](https://uml.planttext.com/plantuml/svg/ZL91ReCm4Bpp2cvzHQKiU4CTOHiP6wczH5QWBHM1Y40lLHxHD_LZNUCeLTDABGSOhsR6ioDMnycDqxplefluze-7A2YjAFz3RVfv7vjvx8VuBOfZ0H_l886G0SsGlbAbTnYTeZEpFGncTFtnC8nJiqZ06fNOqaiqoczCfD2xTkX_uyKnbmQrK0poYyWBwg5gdjlTt8L4ga0DXbUACCgj0599BZXAuqTGeUR0F4nW0zTEhNi9AGkjCWqYCCadjRhqSCqbvFmXp5EGAUOUVzlNZUsZMrmob9GcznQbiOpxC7JOs8JdmOBBDGtvrqgYcOPwADnJM8TgtQ3n1UCQq_DUK1HReIf-NKJITbrphhcYcCiDjWmtWRG_el9-rzODlKBSK0u1ZKR3iY-aAMSOOWiqgBJALSPJpoenivepOBvs4boiiz6zDhMhh-nph1ORnQWKvXseuZ2ozzk0Jz6gwMl_ttu2)

*Código: [enumerados.puml](docs/uml/enumerados.puml)*

**Restricciones**

![Restricciones](https://uml.planttext.com/plantuml/svg/VLTDSzis4BtxLw27pCHJnH7js76QZuaoLbgrYkIHvHnwWoX4Ha82B41gelwR7dl88PDRhlfZVGk002KhdSa7J1ABjsyVTfzVsvgRkYcBev_gEr4ALXLSgYEbQy6uPRD-TiJOYyMhsKa_U_5uyOezd4smFCRJMyLohPPDNcl3h2uqgngn5E6XD6mj-KTX6HuLsozAS5eMh9IsrAoMbKQClnh1RCFk-48MikQvt7vJFOh02iuAcGkLIyw4GgmLJX7k-GUHtt4Novs0C1jM2oM5Mch6SO9GshBJZF7jLsrFZl2SKK97dRHEOrgdAQrJIck8iE9k-oLla16mDyeVW_K2O8NPoyBbG6XTZWwC8589B2s0QNezd0tce_VJmrZE8fQpXEMCi4ma7PzOLkxd1CyHhYXvmE9MhXIEizY8Vrlw7SM22iGhWeG_1O2BijA7uJo9S9uaE4y2DMhxlHJ64OEI5QB62Uy7uz7Lz6Q7BDMgmJQLCDnWDpZAUOKQB_dILo_cqK8_eKCKHV7yxiM2J8X-v7RlR99CB0l22LN_8BtpcDvvIk-SqhiX3OEgDRQ1fdW6JWvSNi-clyu6xmPquePn5Tv2mPONo8lmZpchkE5BKYZJHguGQaF9Bqb2jfPAEoaf1cAeSZdLBYbeJzZuGHk2itGh5ougaPHkN8NnSkd-kWCEPtuHCxz8cL-4mbP6v59xf52l_E2111A8bW_o8s_rK-gI67Pt6sNLPMNuBmWKehmCYsjj_YZPP_RmKtabx59CM_oUXaTizxbFxvWsP7Y9p_A6ZZdk8NHe2XriUK5b5lwYYOZfuV1cVdir6apcGtRQVz1Zlz--6OrHCtP6FuqcR_5gD6Rd_GV7eM0XQFnGV0QGr4ekpdqhyUp40FubACXE3nF-D1B-D17-b0WV03rNkIZG9zexqsEr9ctRve2-Asv1FcU3oIqXFF7zrz-FjwC9llgJWCUhqaQtuZ2qomZjCa6xJ6szDkWNozt6ph4TU075KM54fGqza6ZIIzUfRTlqK7ibg16vwx-M5lV1iehYDGnTlJOW5vTDBupeDYgV9dqdYeB_e7S_YzYV9UpF0gr6hAH5UCShGNN2ARV_-4gTeqMeflPrCm8VMdVdLzH2EVRmJZhKI8LlyhFXV3QucVRQh76zFSeTiYW5nnAzeCCF9f3rsmIoVamWwtlok-V7qmVlXxFHm8MNPOMvo1dVdGHK953U6Jf-pzNqES9ouWMLN0i8eiTsUk-w4NYEUFy_fR9e1B9a139d1CPKzrZq1FfsFdrpUzDHK3kfV7_SSmQCeooBxNVRSxCYnPdFXXCVPFVxseJEQd4bvyFPk-dLOEm2bGlKb_OU5b0MXt-MXd_M3l-hoSWn94ek2z-0bIv9fpJVR8DMGS9LRFjToPoES2t4oO1RiVtAmo1ilyMiLUIAC9XAe9Vu_nBtq_2y5gXAsD-QcbR0hVTecrmQg4IWjjWhTHWSYDJmud2kqLnaoLnapboCGLA-_RQ4it9yXcifRAzJpLxBU2z9XjR-ocvYewAYYHrx08DH20Ynj_SykZhNJ4jU6nULt-CKg9ma6kj4jOZoINkUG_VM2G6VADsMekCoTcx9FXHESzwb8U80f5lzgNHWIhxH9ZIc_c5serVAabVAMg_KpcqdqOxcgSc4MO_eFcLWW8Aio9jQhZMH9_xcv6SH8MjjXP4bDQbWBxok-h6XRR06yYKDL9RCmYRSXpKlN3OvyXIgDKmEuqdeFBxiiE19RQ3IE8VY72LMDfXmSbfj6hH_ujfekEQLqIl3Inv_1w0zuUPA1RdADRydV0XfvVgg4HWODT_nayBkYvwkGZ1TXoeJRLwMR5xcRDvZT-9lsl3hzgINB3wxSJ6J2MC_jmJlEXY2u0j1UJmEe-HTg4Kdu4oW-pvYzmuDwIVEVRfkP4M3Hr99RlA0Hmn6mErg6qrdU4kKtxLEhFph7LmdNgWGslRV7G3xzlq75yTZYYaShaIqdLcodTd5mMc7h3a0agc8veid4MZc9tJdm7j1874CDDGqfh1sSuzHhp4vEIBVY_6-_RQ3-pLMzDlmVm00)

*Código: [restricciones.puml](docs/uml/restricciones.puml)*

---

### 5.2. Escenarios de prueba

Los escenarios de prueba comprueban que el modelo conceptual representa correctamente el dominio y que sus restricciones (R.01 a R.16, ver diagrama de restricciones) detectan los estados no permitidos. Hay escenarios **válidos**, que cumplen todas las restricciones, e **inválidos**, que incumplen una restricción concreta. En los inválidos, los objetos que provocan el incumplimiento aparecen en rojo.

Para no repetir objetos, el **escenario 1** define la red base (central, estaciones, línea, paradas, administrador y controlador) y el **escenario 3** los viajes con su vehículo y su conductor. Los demás escenarios reutilizan esos objetos y solo dibujan los que intervienen, como se indica en la nota de cada diagrama.

| Escenario | Tipo | Restricciones comprobadas |
| :--- | :---: | :--- |
| [5.2.1 Configuración de la red](#521-configuración-de-la-red) | Válido | R.11, R.12, R.13, R.14 |
| [5.2.2 Estación de tren en una línea de autobús](#522-estación-de-tren-en-una-línea-de-autobús) | Inválido | R.11 (RN.11) |
| [5.2.3 Planificación de viajes](#523-planificación-de-viajes) | Válido | R.01, R.02, R.03 |
| [5.2.4 Conductor sin la habilitación adecuada](#524-conductor-sin-la-habilitación-adecuada) | Inválido | R.01 (RN.01) |
| [5.2.5 Vehículo asignado a dos viajes solapados](#525-vehículo-asignado-a-dos-viajes-solapados) | Inválido | R.03 (RN.03) |
| [5.2.6 Compra de billetes con distintas tarifas](#526-compra-de-billetes-con-distintas-tarifas) | Válido | R.04, R.05, R.06 |
| [5.2.7 Venta por encima del aforo](#527-venta-por-encima-del-aforo) | Inválido | R.04 (RN.04) |
| [5.2.8 Control de pasos e incidencia por retraso](#528-control-de-pasos-e-incidencia-por-retraso) | Válido | R.08, R.09, R.15 |
| [5.2.9 Controlador fuera de su central](#529-controlador-fuera-de-su-central) | Inválido | R.08 (RN.08) |
| [5.2.10 Avería de un vehículo y cancelación del viaje](#5210-avería-de-un-vehículo-y-cancelación-del-viaje) | Válido | R.07, R.10 |

---

#### 5.2.1 Configuración de la red

- **Tipo:** Válido
- **Descripción:** La administradora Lucía Romero da de alta la línea de autobús L01 Sevilla - Utrera, a 0,10 €/km, con tres paradas: Plaza de Armas (orden 1, 0 min, 0 km), Dos Hermanas (orden 2, 20 min, 15 km) y Utrera (orden 3, 45 min, 35 km). Las tres estaciones las supervisa la Central Sevilla, donde trabaja el controlador Antonio Ruiz en el turno de MAÑANA.
- **Restricciones comprobadas:** R.11, R.12, R.13, R.14
- **Resultado esperado:** Las estaciones son de tipo AUTOBUS o INTERMODAL, compatibles con una línea de autobús. Las paradas tienen orden consecutivo desde 1, la primera tiene 0 minutos y 0 km y los valores crecen. La línea tiene al menos dos paradas, la central supervisa al menos una estación y tiene al menos un controlador. Esta red es la base del resto de escenarios.

![Escenario 1](https://uml.planttext.com/plantuml/svg/ZLRDRXit4BxhAHZtqWAMkkHAZXsZXLLPG0F_mebxycMyIyjCkEIMv0fmYZv47o4Fa4FGcwzwiSvmj_R-o6qkbZ4VzvkFC_FDxf4Fu49LwD6hS2yBoKeDoeo22bgofS-a0QSiuspC5jRSgLNb85ERlmtB9TF0dCpPz-lDPwro-yDeP6y_o2omdIpXx0rRI1CSw1q6db7aZn5ZcStLohAVsCuYuJiOCBQuTJ86wkFidLmhhO70J5Kvv0IsWhboCikKDHIVhoSavGICnomB5HuO5tGgI2tlh8cf3cRJ94aunuH_jaG6fwDCYtcjXjowMcgCazZSA7eyJRX8fxEzqwxUkGdMA4j1AAMc4d2ANrNgKpieA7W6RlF5KFXEPlTmZbKi55wOzF63r_jZpiU9wEdUurm8aQOfFISBK9gYKEUTECnpX1KptbK19jB7Yj5bmClDLuWrnRjy9nAn1y_QhtqLcqfKhj5gga8wkonABR4E12tFBiS9JvuKlpKh9pr10ik1MiTyXfW7tP09dYIJX6vGkIZ_RBxvQtu-RvTSnhbOuj1H_sArvM0mblt1kDJm2QZBSrU0_xQvkAomk18Vqcm1sRrrpZB_Osn0K2NbcL-_l_Zb-jtJvUScbqPwHFQwaiLGiXX85bt9nzQpNoNADNt1VMJRDACKq5Ms-G9ichGalpr_lxmwkpYUdsvHFUsgJeUgqu7gjAlwEZZfUiPxZdNhYpDcIIkwnwDvwgKWlgc-QLifv0NDn3mlb54UP_TV6qQWvKAn_tflDat5ILVpQQpJq8IMSWvi-9jSmKict4_6IJhc0nFEfZpwi6D2R8-2YOjP_i-4U3TkNf4BjvfGkoUjBzYmDiJGXhDnSe0cxTfmlx5XBajSxL0qzoCN6NMdihfLdKseeorFbP4GIwy7dZpjUxBn7RuNdWTaqFWI1qRPI-jE2emd4qww80jgJNK9hf9j5IM9k0ISW5f5MOkm3kU66eJ_uunKmVfZwNDvuTGg0aJwiUZ5i0HjQj6Z5XrgyHArsCRDPptoj4UUTiZJbyYdiotaQKDUluJ6OrQ_Id6SRk43C3ppyuX2YDJBr5UbT6lbeGs8bu2q1qIJ8L9Fm4hwWFucGDTFn9vajYWhX82LrhKmyHzOkWKhsx8byofi7fih8TUpy1wKjgHteP6nkEDt5XKPXbrDED-bloB-JTa3_KpVCDciGLm-pMmoszgbn5NXfJM-e2NSu9fYWDswtJpwGrR6ldgoTDDHp26GEjsbxv_IANGiC609DHdZ2ROKVzQWBHhrncJa0NeN8Pd74oqnJfOoA6G_P1cK6CMLkykWsPCFZNrYyJuMDwQ01w9alrUI9OTCuR4G3Pt7yERHgCpISwtrusb5upSLRW64zZ4FVhy5YUSdSLRF-UX8cfm-0Fy1)

*Código: [escenario01.puml](docs/uml/escenarios/escenario01.puml)*

---

#### 5.2.2 Estación de tren en una línea de autobús

- **Tipo:** Inválido
- **Descripción:** Se intenta ampliar la línea L01 con una cuarta parada en la estación Utrera Ferrocarril (E04), que es de tipo TREN.
- **Restricciones comprobadas:** R.11 (RN.11)
- **Resultado esperado:** El sistema rechaza la parada p4, porque una estación de tipo TREN solo puede ser parada de líneas de tren.

![Escenario 2](https://uml.planttext.com/plantuml/svg/ZLJ1JXin4BtlLqpJ2qXajKusL4MYWWQuW082qXENmJkbRhtsrlP62bK_fX_GGzKRr_nOnxjRo48gLOgqcJUstvjvOn_uW2xKfKvUXKzK4bGQbKc22fhWs4iow9I54Gmv2YZLwhU1WY0uCi2_sY3erKz3656iWxrTFNZOKcQn-g5LORUJnDv-9XbWe3C1Ut2cUF40q4ECloK0qXRgpi8-3CuoCM30sFBMKGIkQA6qHcQVCwF3c0sgYeiFvzVdx-TN35IEfB8NrfsMZ6Ufo1X46TG2EGwkfkJxaufgp28kq67Hgk0uYh2ku7BsOSp_IsMu47z4lg1pf-wQH3xXp9Uo3uudQTOxF7zsU0wlJqwETezsUoJvlqZUP1j89gC-2MMnXDOEQngU2FLRUPoD-wryQg1KTO557oiKDp4U5-4Bz6IZdrDhlDMTwwEDpJTrISuU6YwCVDE_djByfT9-IzOLvviKmmavPoKwf_J_gTTutqmZpytJp8erzTUNnxCDqaVhqcKph5ComQ5kbCiNupfzFgxTSkZ6jYzt3UpfFLoaKUqf6iyiKnMM8DfUawQFrZHKRoTvbcL2C26BZ3TdE8HcbAKjgvguJgYiWtUFkNn3ZZTmgfqVJ-1LM3rWlCQSojjK_Z8bsnExRNL5Rg4yhYVoPud4s40m-A1uUE0o5GAsBcVysTwBfj2ZAMmvHyrx4fs9ljoOPUJ7rIzac0kmK3NtgNEqUspufTY1hpLrsxjvJ1kZPYAP2KXJxa4dv2nUnUOK_WfOGZNQ0TA01Tz2vTcpWlkpZ92qR27BPk36j9IoSzMpNzf6FisqVvz6qP6EaWCoHNn9_m00)

*Código: [escenario02.puml](docs/uml/escenarios/escenario02.puml)*

---

#### 5.2.3 Planificación de viajes

- **Tipo:** Válido
- **Descripción:** Se programan dos viajes de la línea L01 el 10/11/2026: VJ-0001 de 08:00 a 08:45 y VJ-0002 de 09:30 a 10:15. Los dos los conduce Manuel Ortega (habilitado para AUTOBUS, licencia en vigor hasta 2029 y 41 años) con el autobús BUS-01, que está OPERATIVO.
- **Restricciones comprobadas:** R.01, R.02, R.03
- **Resultado esperado:** Se aceptan las dos asignaciones: el conductor y el vehículo son del tipo de la línea y VJ-0002 sale después de la llegada prevista de VJ-0001, así que no hay solapamiento.

![Escenario 3](https://uml.planttext.com/plantuml/svg/ZLN1SkCs3BthArYVaaxa8MNPQsUddNYpcJQj7NkIJKvxGIGaPeOYlHJbcKsd7zDZ3pdiz1FyOmKeEtPscxONoteaW0VW0Jgf0_ZGL2RvBYomGh4qe6qIT30epke2BNZjH4-aOcx0wZjTGA7NVrbHebXfUC1Q7ApMVnXTkiCaSRSFM0JHCLA9Op7H5g4Ze1Ry_biYHE5AVU_43w8paQf3W7NLhKS6hd2bZG4AT1qyUk3Je9TyUNpzOVRk-eg0fST2kxdplrQ4owwI148Hz0heFVW6azztB5OBPd632rqqnaKY3BqaGexJbiitqIe8datPTqTblJoTJ2_uPkLADD7wtEj7y48F62vWISKfeQIJVf-0EomMC2u_DRhMnDiIdiaiIsLE8UaSgVeb-vdDpox77yvlPllq2sTBJk2KdaqHd8yPj2ZdK5hD5B9HtX-y6OwcB-iv1TkWOGoMQBW_Yk6P3tWF-t36yFlr4pnsjfGlA8rAemtCJOs6_LGEqenh7T3WdRCnzq6MzNgzFC_P3YlGXj4gnksw6ESaUB2rRm9qiOubSfQW6jTVOb4vjU-f98DgH_wwRgBcs9TxPckR2hqxgvO6sv9rpgRpL6RocVIvlVTOkrXZDQG2ft98PpMOZJirwikkVFOrqQJkGaSUa_FJDC_VH1aiu5OR7M3Ji9qSOfHJe5vmZ_VCAO5HAljfJxrGtqDK7yz8Axs7hwLtyqigPQkzwFjdv-4Ag2SRfuDKgLH98OV7KkvVcXYgIWbpJpD34lhcTjxV5zVySlRJvNWwVZzxII_xYbxsZ_Io_wOtEkxzVtfA7gj_eSTLIbFHBf0bKlyj5YX0_9WmmrUFebKxy1w9v2Cx9xNjx5uxZ9RRMIlYhD5KYANpEzlNZXFh0fAC6zQbkEnATSI_cVZCZzxMW6RtMLPi2nwskeeqp4PB7orQsghtTFspo9M0zHTNlnKhNAoVU8NjzWIltxYlDcxUnlwHrb4Sn7uSVhGbrijc_MVDTsc7cROhl4FRjl06f-mFec0EZqW4woVngK6QGr6Zg9sXVMQxKHqNQfFg1AY4fJWWmZJ8vYZsaKS5wwEOM0dq1yfAMmf2hp6NEFRaZzw867qp38StPBpzmgXDd7V92LB9AfFy3G00)

*Código: [escenario03.puml](docs/uml/escenarios/escenario03.puml)*

---

#### 5.2.4 Conductor sin la habilitación adecuada

- **Tipo:** Inválido
- **Descripción:** Se intenta asignar a Rocío Navarro, habilitada para TREN, el viaje VJ-0003 de la línea L01, que es de autobús.
- **Restricciones comprobadas:** R.01 (RN.01)
- **Resultado esperado:** El sistema rechaza la asignación por no coincidir la habilitación del conductor con el tipo de la línea.

![Escenario 4](https://uml.planttext.com/plantuml/svg/PLJRJkCm47tFLvZk2qYaSfAc2qYBA51OTakfIiiJBqCoK2F7pZfE9LZjn_01FAps4_fZEtPweKAgbFhOdZbpvenFAWl6reKCTkmC2sIb1A42Awn4rgyoL624PXqMiZEjyZgpsh1AA2Q1pU11I64X4uj_Ya6EMGqvi5sXveitANAz5mJwuHaporgIH-o83OH2Q36ec5l_3XZBT2wUDFl6MWCUjGXGkdWmw81Rd0j9QK8sjGODk5qhIdUuDvtSd4vl2IWDPaAFjFbP4CxR4ISGCYlcG6jhQWp-R5ZCPux57Sv4LaljYJXecmW53Xikdx8LO8sxwcAteZZfX8FheJjPw1obltrbn2iO9heEpg0aTNB8QITD2NZ4R0Qz_5SjAa6y5U4nZ-EGToWbxIEr8tTnRaRzSMzoTNVpaNv67OYfW7KdV0KD-kNYuhnxtlMrv4euAacKThyU7FBHjgvZdItUjSEWHEdw53bu27CmPWkF7NufP6l5V4ZL50ALTHIZmqCUyiEGky8iIdpKoalGJP9EfvEcgRk71GZfKAEfuBPgifnO0weojOKsLbufhGYgSF7NQping7P8cQxPS9zMjVUYYwKsP5LTeD7zefJOIDVgNuz27aThrbVgoM2blTOHyKt2o6bTWLp6YtdAstmTQo38zfdmH0PNPs5w47CNREDttxZ9k3zSfJW3QeXhz8UxCKz8doXCeYqBFYVEWWAUiV4VhRVzT_SZv9mdQ_hVjO5Re7uqGUCkqQSVumT7d7yyD93uH0CuCZGuvADFfplfHuUDnZUNuzvrxznRpD48GzQCQOd6ei8C6R3ZrLOpEmQ9oQkBG1tRRAwScNbdadLOIU_4SH0eRP5A4ffcPjocwBlZ8NtsZfPNYCxYRQqibLY20I_jlNfXADdSAULLmehbwDuTkNYdPuJHWx7l43-bonbjUqs7KJ2CMBlTv5Xo602HpzakzOUS9lUz4gwLMEtxh3dG7yWBeKWzMb81BuqlYJojzkxfgCjFeqwNLqzZjCnv6fmW5L_8u3y0)

*Código: [escenario04.puml](docs/uml/escenarios/escenario04.puml)*

---

#### 5.2.5 Vehículo asignado a dos viajes solapados

- **Tipo:** Inválido
- **Descripción:** El autobús BUS-01 ya tiene el viaje VJ-0001 (08:00-08:45) y se intenta asignarle también VJ-0004 (08:30-09:15), con otro conductor.
- **Restricciones comprobadas:** R.03 (RN.03)
- **Resultado esperado:** El sistema rechaza la asignación de VJ-0004, porque los dos viajes del mismo vehículo coinciden entre las 08:30 y las 08:45.

![Escenario 5](https://uml.planttext.com/plantuml/svg/dLNRSjis47tdLw1g7-IEgG6laJpJZkLBMYUwLRuyzGKcDnOq8C20e6RiJ3uc7v27VeD_h0jGTyj9sYUI1zpTitiMkwULOThKXV1-CZCeW9I2SUaPRWIGooe3oJHN924-kOFPyzUi5egmYZz8bkCBoLL55fpDeIAL4gna5cXpkNZ-8dYkZZnFtSyXCwGbQ41Eo81BO2tqGEpt9u-GJENyGP7VI6j0WnO2KXNt6Ynm3Gik1CFOjqQ3PlRKyDB-tB-z6P_TNYDGQiYucYZzla2STWAA8CiCNp3yDhe6x_E6nM9cMM0gt6RYY5XebmWwzXikBw8Lp6XhQdstWZ2A_S5mPFyiL0x2MLzf_iGquQc5Con9ndEMuqcI8F01iXdhvnzhNd7aBH4FQHZwDCQGU0ueI6xzZ2UNq_xDrTructwcP6uJECTddHcbNGODQdF89RSKmbwSf6-wlU5kFOTCrY0inaeGLf_0mcDju85jmw65Bvw_igVMYl88qoWuI6Ev1RrkujFK3ssj3GZueAJBFGt3A8hYEBPsK30kB5guk1tbufmQpMIbQyCwKBaIAObG1S__kABQr7x5agJ5XlnjLRisjBxKcgsi2z3giYW5D2LhNGudFWtfclILVD1GALVZe8i5zcaNpoecbkw2Na8xTErhmB7XC-vu3AxE_JX-uzfWnkwvu8Oj1TkqWujopb0Bg_6MEIRGysdYHy6-VD51-Q9j-QBGYaU3ltRbUySM7FGB-OPASFbIlXkbTLFWW_fr_I1iEcv7lpXEaYHDqntzvYvmft21lgVVMLtzOkMR7zPlNgpflgPVU52_aDgESun_MB-KHl7_rWyhHFVqMypTzB3phXaTy_tHSVVEfxIP7SxtdqgpQuQYB9scVX2WNqAx9vHk_pGGsDKvcsYSUJW2NlmT9zl3OJ8T_p7j3_iNurrwyHwzcFpyzkr5Uf4Uf1d_2CteFz3idGJVe6chvVkaMGGbu3sMa04kaTyzo_JL8sVL36uDIFB9EiUkszYzTkWiLpCpSvSEhmSfbTxOhkxa_h4dbG6i5CUrHgOT6f7sT8IFenC2WYpMox1P5hWfaF9wCpQjqNOg-qwzex_benEaxMhgkreT7PEFDP0AbijJ4hoWUDa5YkT-8u-hzpZfE4r7WJSAIATZVIqf3XYcdfDsPesPE7RrjwqErJ4fcKQzy8Nb1PUe6NvYVe_DjSMe-8NSS7zZchZ2q7Yrz8Dbn3Fl5B0sXV3-1G00)

*Código: [escenario05.puml](docs/uml/escenarios/escenario05.puml)*

---

#### 5.2.6 Compra de billetes con distintas tarifas

- **Tipo:** Válido
- **Descripción:** Pablo Jiménez (tarifa ESTUDIANTE) compra un billete de Plaza de Armas a Utrera para VJ-0001, y Carmen López (tarifa JUBILADO, 71 años) uno de Dos Hermanas a Utrera para el mismo viaje.
- **Restricciones comprobadas:** R.04, R.05, R.06
- **Resultado esperado:** Se registran los dos billetes como VALIDOS. Pablo paga 0,10 × 35 km = 3,50 € con un 20 % de descuento: 2,80 €. Carmen paga 0,10 × 20 km = 2,00 € con un 30 % de descuento: 1,40 €. Cada billete guarda su tarifa y su precio.

![Escenario 6](https://uml.planttext.com/plantuml/svg/XLRRRkCs47ttLx012YI0fLAK5L-A5d4kQ9Dw4yEnynIWe2KcOPOIjHHjDBleI_-aZmMw3uj-Wl-aNz8PIdOinUw-sDRCyCpHcGjzKbXkx29LhM_iaqW5oHMNMSjAgmIvA6AHSICrEIOUETDfRZX919bBfOGL1ObrHX9PM9bPNX10aW_mVRXS_QbaeezQBJr_5h4b1x6I0HcGCoL5PiK1WIXd-jGY9Caa-O4STBkzNhzFwVG0R9bEvqQWUSpdIgE9vq8XQe3MAvck_ih4nsq7GySPBwnnyGyYVkBNF9OfvjJWP9HsFCgye0DkAvHuq1dQ3uxx_I1WB0pnd4YvL6ZDCRF_B5C1YKvYROpGlYWm1jxR6bw8rHU-uUWZ_2ltMR5moY5GXR5XDH6F4cawJYpoQDyBA79o0eBrudOwExySNaylMh_NH6HlHMJR8h9k67OwKJIfYtZ63J1ue-9ezK--GyEnaSBkqJ3eHy2twuMqgM4KzVkzNhTRqp1sYNsbywz8M394z9qInWvXZu1GrCXZDG6lPgUNe-7vpRPyosTimJl9dqljyFcJOvB8HvVbxigZb0QRD3zhmsyvwCBBHCTU442b2EqDADqE6Ydno1C-Dc89lDv6To8K1Jm9vXbFRdwQ3Dyrw2cAz4Oo4zpHm-SwlL59xRMijv1EAGwpERD668vUAtCC7iwcDwUpMp3aHiHIZxNv9GKxzLsdyTZA9VAqPY6sMUH8OimDl8rZaPSaj4b4XkM7twdC5bONvw98n8sHZyw1eE_JXisdDMZMW6OrQBOFckt23g86UDW03slWuJvmLvacUDW0dwCef-NIS-ZpPcbEiN6QhLCjpDTce149UWCtEhpOJ9HxSHYuH8-vGnkEnx--kxcx7ErP2UkQ8hBVezkjTJSSNTRRQiwQtDbkxkmht8z9G0T1LESUDhbFXvEhY-bmvonkyGxypXxU8AtdaUg-Y1qBYCYr8J-sW7hfOpjyxbmvupdqmIQyRd9HhibrMUsD3Hie4NYNwSrPrxAlWMn78CA3jPpUN1WBcpuM19EXRPqDsIxmTIkwQn_x7r-umzVAj1MmBXTf3XVqnAUTDdv6v0M_ZiaXUxsTtJj74Lxf7tdHThTrLRjYVMthucXmdys30Q5js5N__l7tT-zJyXiv32DOA_G8PWC7eXqvt_TRRKXWvP9liKFQFUVq0OVjnWcY2eYrQGMqxWe2Luc32TeTvxhF3bs5BH0Lf1iGlle2BFroSsyjptRL0eJ3RGZtX07HOHaMRc6txxFSROC2_x20MQq-msQ51qMM3kXbNLWyNf0F2x6kgk_cv3gejBuJJwlFyK9ffs2oY2saUY5690DeuEeVKa42i8NLqTFMYO38LBN-0m00)

*Código: [escenario06.puml](docs/uml/escenarios/escenario06.puml)*

---

#### 5.2.7 Venta por encima del aforo

- **Tipo:** Inválido
- **Descripción:** El viaje VJ-0005 se hace con el vehículo BUS-07, al que para la prueba se le da una capacidad de 2 plazas. Ya hay dos billetes VALIDOS y se intenta vender un tercero.
- **Restricciones comprobadas:** R.04 (RN.04)
- **Resultado esperado:** El sistema rechaza el billete B-000005, porque el viaje superaría la capacidad del vehículo.

![Escenario 7](https://uml.planttext.com/plantuml/svg/jPNDRkCs4CVlUegfUx61oY1bony1MaH9d3PfrZ5i9wTUQ6bYCv18bQ83x1Pzc1vxs4EVmI_M6Sd9sia6BL3qbEZFcU5lFZW-hRnoVblamJT-WmL2cIjj0gzzZZ2fKZJAQGj32E4UZLTGMWTeKbqeo307zM2TXROsJxi_SftPJX3OrIEc7bfFcmXEo6kZqsrkMw0gOEct022rcLvR-1vQPtUBK0nRf7bTifBSBMz99A5Gth6hOhjuC1o5Fy_cR5bOkhds_e0knGmhM9HE6uyEsWsGmsoR4WunGkcsk58TzanLgKXM6Jb7zFs0wKObsQzRNUbKMyEoYFgXY48fw1ofEXdVT3kRp9FbrVrjyFj1Weyn9wZL8pRPqVTnTlVNeH0YRZrVzPDrQg6eJggvQH1A6Se8nF14Y4EZcnpNAbCpXq-Q6DvOz-D3kjdyziTvyY6vEC9BSos9xppNrBY6i9OOCJEQ-OR3qMWy5cB9XCOMAuSipzIA6aQIAZ7dhafMhtMn-zFWfyE3Y0_ELUMTVKboIYKk-4vROukcd7rkCURuO4rTci5uB6KKzNhiXuNIEQibtznzr0NIHQUfTGvj5wkwTzPufohS_QLU6BiS_WlxNRMjfvK3xMEyKCrnhHcpAMKSYd7JOXf__S0H9ulbtSLLCbrE3ekuwb49ptIUuxw49BoQO6wn4BsZ9f_RedI7RHD34EEcoQfwBexaOKPlCpjJTR1bCh-UB9Dti8166TCwlwWxEfhF--JcwhZxg_vhyFxNmVl_2bmUWKV_9tZy6Zo6RoylBmONWwyd4Fz30YEGqKbFl4QUN4xEwnVz7vZvnOSXD9lE8RtiJsn2eme_13GfV5OlYL8H4qdzjrByLgeTzyythVEXeBnudoEySnQ_VnOOwv4MahQqJ-TTqOVsV4f_EYT0M_o9TrWDuQd523rODGseziakVZ4VQqFSx3xpEkTXM1kENrdwQjo-hDSwawXRzsygWwc4RfSQkQUusNqsgE16oEzgouoiIWbjw_GQJGS-Gjc3Dkrrhutjy0xFRVM2n2XKOLhmdbBZ_oGbyVnJLO4a_zx-whFW5Ccoo8E_0G00)

*Código: [escenario07.puml](docs/uml/escenarios/escenario07.puml)*

---

#### 5.2.8 Control de pasos e incidencia por retraso

- **Tipo:** Válido
- **Descripción:** Durante el viaje VJ-0001 el controlador Antonio Ruiz anota el paso por Plaza de Armas a las 08:02 (2 min de retraso) y por Dos Hermanas a las 08:40, cuando estaba previsto a las 08:20. El paso por Utrera aún no se ha producido.
- **Restricciones comprobadas:** R.08, R.09, R.15
- **Resultado esperado:** Las horas previstas se derivan de la salida más los minutos de cada parada. El paso por Dos Hermanas tiene 20 minutos de retraso, así que se genera la incidencia RETRASO inc1, reportada por Antonio. El paso por Utrera no tiene hora real, controlador ni retraso calculado.

![Escenario 8](https://uml.planttext.com/plantuml/svg/ZPRDRjiu4CVlVWgkzz80iKjHTZO9i4LSrq2pcoyu7wS0YuaqSHZGeajA1jB5FaGVeG-GGx6tlVh5TeQI7SbsaLmSP8QQ-SsVCoIFV0ukBwQczKl-W5CKCmCwQ-KwDoX6Fi4CdBPYNtJ4q6QviqQaj0YyzGA5pXAT8ls0c5ad7EQEFEBTVF7TwDJkj5hsxX6JNBIJt9b872w3G6fTMu0NfVtlbX1ffiNleXtBICMz_jv9csoPdTuvPFCWosscBHjXXeQZHsmV5_fhtQZOU0fkyPon-HwJ1pY3H4yrKWHoHWU_xNUYg2CLkNCqU6ypjhVteaWf5SSnVuTJq8QjKERjEifpHDLbtXKvTD7pgeIB0O-B7y1hXrJBhqggFNXXl_P54913kOerAwRex6WwCqWwi6jqUj6HaLmH7sSJXvvTYkGWraxK9vy7KmLJaPHToHKKBk2V3XRV1cU3rZyroMKG79dHb6BBKkh4fdeI4WzbrDIvMYukSQwD2OKakaWXPMVDc6g7IQ9j5ZPdtkMk-9FKeIn3dJ-9IBNNz3tezwIKKKG9Qv0O84VKXHmiKE86vMYTyiB0L-2K0pS5_pR8Yu9AG_h8Y24a3zOvA_pxq2Mvdd6UmVNL-SVhozKE3JBgRVJasMiYgqrajO6iciYVQ60-8-5cwy3hdctI4WgO8bayW-Z96lBnsTLeV7h-QN2oXRhNf8utgUCDwhX9VPqxT6jTy69hwak3O9cLHjpJX2TwyMycr9lqZUkayqTkYHiDZnWm-VycviqV7MwbrRXyjWukWSOVmiIeFHxmI0gvVoXbVT69mGcaSE6eHqY9ZTKzdZ8aJnZBqTbVm-lnvNaTRkOPZcgr3IdPp8plLwb-cgD9D4OmcqluR09M9sX3OQkJzLHdHMvPCTM4KjkXr5kXrEjGFLc3YhP3oIPLl9qgVYjLs8vboaR5TF7mFXolxfyGEbWvjauhnbMExULKZJaUNOq7jCz2J1pCiUppaz7DY7iSVUBqRDdbu-gAepvNKaohqWbmVOhf_2xGXABkmNXa-lAowtH4UJHJf3ju1FxwGujDv2dFGr_Cq94cK7UedpdYDGSt8pdAEN8u8Mr9W5c5pNxrYZz-nLz33V1Ln9AniiGlS9MbBBrw241cmvZnks4LOBipR6lDwv3iv8SrVmaCjESaEzGS9PTD2aUdMe1fKGAaswxWIrYCkt9_btyFn1FzYVg7me1uuBQPBNkJpYco-VAKUHVcU4TC5z_9RFrjjkm44YW9PE0WBDpbFdaIlVvEDmoauIE8NmvL__0NgtVJ1n7rTtPlgOKMp-9BWSAZcD0PII2X5beRMhPgsDqrARuKzCefnHDqpV6ETMyptWfO_9VHiKvVKxIoCaT3VlXYIruUPe9UOaka14nIi9K2NTaKM0KVZaYU21B5sr02QPDKJmUMW7Ug6sRoBAgqFg4d6SAkcEF3ufbYMbA5agP5abDA-eYsZrfb-UJq8g85SHNaO-i8QUdKjFu7)

*Código: [escenario08.puml](docs/uml/escenarios/escenario08.puml)*

---

#### 5.2.9 Controlador fuera de su central

- **Tipo:** Inválido
- **Descripción:** Elena Sánchez trabaja en la Central Cádiz, que solo supervisa Jerez, e intenta anotar el paso del viaje VJ-0006 por Dos Hermanas, que pertenece a la Central Sevilla.
- **Restricciones comprobadas:** R.08 (RN.08)
- **Resultado esperado:** El sistema rechaza el paso ps4, porque la estación no está supervisada por la central del controlador y el viaje no pasa por ninguna estación de su central.

![Escenario 9](https://uml.planttext.com/plantuml/svg/ZLNDRjiu4BxhALZl9G5YbrHinmwmHRAsWxRhk87TzbHWCP4c2GEQz9AIWQJOXya3vB3Fu1VR6SfnfRW5zYHeVh_vPePp5WhmHRaqoPlY3fSeLWQqJGfT61IJaA45hvqOYhOOELjuPo1tNjoKw47aA48foARmOCI1jklDaz6vEqmITtsFMI5QMU5DAaxhpYq1GLJotouknlrnNtnFXCYj5h-BLhVRw_Nx9oUB5iciMrvxPF74418MmGeDvr0iNMoUR7Q7ZtL5oehvvja5bjvWTWSpoFHI4qn7EZKS7BTbjwq6f2xGu8spB6zHqi5WE9II_N09sh0KEN4dGCnpHeNQuCi2EXZ3PrmM1Dpy2smyWbm_labbsiSVyB-4CdB8mRPneh9SedUJvSeWiH9hlBngIwLsi3_OMu-1LQbK_RQIRNbCkW1c6ozLKdOabr7wMCFdy_bualnJerz6ygiELSIJv7j4dklRc7OaqoRNMtCnsZnHER5CNUQGHzrEbckFMQQTPV6LWSSu3mjSQsD0n2hgx0vxVIbbcbAk6ZwKFS8teI7aI14WYve09xBN1FWHFJvoDafquQa5D8nDcB-oQ80UWQ4X7skaD1VeBGrJJbxlqNkUxl3MnYBqYb5yc7sUp2y_ZS-dkzwTaxd5GCfUatErnxdQurpzdFCjVyrgQi960UVh3jVu9yqaPHdfua6eT9_uBX6lUAuQnAVxnATxnB-QZB4BJCyIB8GcnDUQd-4ali2KsUOPH5V-NsgxTTJh-pwX_ghX7YDa_cz2_lgnJTNsTplqtdbO0BqB4DSefJLIj4b2dPnAMJUQ6ho57AuySKsix5btU_mga2RkwcJsr-ZBVF6f3cuLkWJk2e9hqChY-ZltTfVobxdI1h8vWjatuPS0c2LMSLYF_1AvIstBmZ5pnmokUcVRRL6z16Ho3VSWoErTmYBIL3iOoXLwWWDHeIf5-ah1jP2YQeF7Mq9FYxAYjDpVdRvoZ3csafSp0DP51bTqGzub2VqXzKpJOH3pZXo8W_cCFeUdMuSQNB9uEJJqO1s9lqiwFiwuR_O7m6hh3-YzE3mI3rKoKTeA8CMe3ocRfOT7DNSQ4OP5gzaHKn1cysmHn9IueDguPDedYbHPKOvEREzC9JCbEXtkyxQUwSxpIApnRlESbSOH7ZeMUPaLvBqD-53TZ-eiOlX6xUJqHDtBuGr2aTNnDjKVoHbIYAL9_WC0)

*Código: [escenario09.puml](docs/uml/escenarios/escenario09.puml)*

---

#### 5.2.10 Avería de un vehículo y cancelación del viaje

- **Tipo:** Válido
- **Descripción:** Antes de salir el viaje VJ-0002, Antonio Ruiz reporta una avería GRAVE de frenos en el autobús BUS-01. El administrador cancela el viaje, para el que Pablo Jiménez tenía un billete.
- **Restricciones comprobadas:** R.07, R.10
- **Resultado esperado:** El vehículo pasa a AVERIADO y no podrá asignarse a viajes nuevos hasta que se resuelva la incidencia. El viaje queda CANCELADO y el billete de Pablo pasa de VALIDO a ANULADO.

![Escenario 10](https://uml.planttext.com/plantuml/svg/TLNDRY974Bxp51rosPKCwXaCDeuIUOp9XXK6X6rEAqN5J1dQwkcUxPb1YbTvY3p27lUmXrKUWHTBLS-0mNOk_5HrLttrTNrLbta1hYXJtVYfM6EA8jEWJADGXKOnp6Cqu9GLWHGj4MtGRR-3I52KHcnmlVqUbzgAlqGC9aODiTh-QyYjnKR18uftc-rNhHBxlj6moqUC2z6C2wS3SI46rXJEQaYiQmh8HMN_qX0YCKhyAfeT6OITqsvlt2IRiUdI8PiZKrYZB1iXGytH0xRFI_LqQ0pPU0E4rx3v0UCrJ0XWgf0Ya3FedvstWg0bGt8NgF716hOtUq4GXc6dq-5xc8BIR8KgRzjHdil2WSbTMK0RSpuLSp6Gu_O7yDa1rV9pACCUF6E_pqjF98ToDLPJfkZiCCqq4W_i6jxCMZAGUyGZitAOioiaEWXhA-YIBmTT1mi3ATkIAoYTXtyJRV-99b7ZxsVADsicV85hnQ_bsMRJ5myyKIkV-Uh-bZ9pua9bR8dkxwPa94CAXUEhlZP-bDRuPi8dKqjFxM-Fd7e29rJFim4P4Pr0GfvkTrTAb7mkLQvYPOql9wHoJYabaqmzwEkF5iFvABgU7g5_31azzrC5dVuVGrzyR4afmprfVrW7jq1D05MY7ZypDR3iNtJauQ6nnXKaC7EuKGJX-7GWBppNUt23Q38OZb-WKoPcU2D31IDzWCVehGnICGVXOSvNa6JtW7DVF_rVETXWnU27ELbP5fZ7JcKrVytVGHFrBBMSi5ERiYeV71gR2pJ4Jhn6QaacXJgeHEthUm2Tuo7-M2klHisog0ZsfWCXdfsTd_VxKjuT2t46I_rQXXzLklrcyBKK1v2Jtj-MOYYv4wYtJrzAiTzdCRwGOiQPsuygHKfq6LldqBujH8-npU7V569MnzYZckDACSpgaRejsQT7Oanquu4Z3c_lxgz7qUHkU4ZYiaSKNYcjiQQG32_KnHqgPU-eHmSspOuQGfxJpmkV4V8TETmUAHOsiJFmmQBPxC-RwM8q_XzWGcGEusewjC_baRuczxi6zYtQQedTF7QOMKV7GD2t-6tlhuQ7eXcbOX1mwF7212gdg1nisDzmI0fyuhJq9kGaIlPtCd2mDzKT67iow7YLlc5iWTGr9Gz7CM_BCzeoypQ9Kj0SWdeHUIYPDUYJKjll1eXuPpXIuhPVFndYSaLRB4T2wVTIJfCNDoIKDR3iUS9gYgZXFIMXO_KsOxGdudD9En5oe3ZF8-0NaPTYMRqulNPrYcbUWbX4uz7rzCHVeemrv-rFPWoYWh9RanwrJyDPr-h92bepc9-8k0H3NDGHfxFXFBeRBQPjtrsJe6PdJCiDuSJpcG1VioOfuu8Of9MCB0LzGK7g9PwBWEoTEiXLunBfTAeR_m40)

*Código: [escenario10.puml](docs/uml/escenarios/escenario10.puml)*

---
---

## 6. Matrices de trazabilidad

Las matrices relacionan los elementos del modelo conceptual con los requisitos de información, las reglas de negocio y los requisitos funcionales. Así se comprueba que todos los requisitos están recogidos en el modelo y que no hay elementos del modelo que no respondan a ningún requisito.

### Matriz de Trazabilidad - Entidades

**X** en negrita indica la clase principal que describe el requisito; X indica que la clase participa en él.

| Requisito | Usuario | Cliente | Empleado | Controlador | Conductor | Administrador | Central | Estación | Línea | Parada | Vehículo | Viaje | Paso<br>Estación | Billete | Incidencia |
|:---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **RI.01 - Información sobre usuarios y roles** | **X** |  | X |  |  | X |  |  | X |  |  |  |  |  |  |
| **RI.02 - Información sobre clientes** | X | **X** |  |  |  |  |  |  |  |  |  |  |  | X |  |
| **RI.03 - Información sobre controladores** |  |  |  | **X** |  |  | X |  |  |  |  |  | X |  | X |
| **RI.04 - Información sobre conductores** |  |  |  |  | **X** |  |  |  |  |  |  | X |  |  |  |
| **RI.05 - Información sobre centrales** |  |  |  | X |  |  | **X** | X |  |  |  |  |  |  |  |
| **RI.06 - Información sobre estaciones** |  |  |  |  |  |  | X | **X** |  | X |  |  | X |  | X |
| **RI.07 - Información sobre líneas** |  |  |  |  |  | X |  |  | **X** | X |  | X |  |  |  |
| **RI.08 - Información sobre paradas** |  |  |  |  |  |  |  | X | X | **X** |  |  |  | X |  |
| **RI.09 - Información sobre vehículos** |  |  |  |  |  |  |  |  |  |  | **X** | X |  |  | X |
| **RI.10 - Información sobre viajes** |  |  |  |  | X |  |  |  | X |  | X | **X** | X | X | X |
| **RI.11 - Información sobre pasos por estación** |  |  |  | X |  |  |  | X |  | X |  | X | **X** |  |  |
| **RI.12 - Información sobre billetes** |  | X |  |  |  |  |  |  |  | X |  | X |  | **X** |  |
| **RI.13 - Información sobre incidencias** |  |  |  | X |  |  |  | X |  |  | X | X |  |  | **X** |
| **RN.01 - Habilitación del conductor** | X |  |  |  | X |  |  |  | X |  |  | X |  |  |  |
| **RN.02 - Compatibilidad del vehículo con la línea** |  |  |  |  |  |  |  |  | X |  | X | X |  |  |  |
| **RN.03 - Prohibición de solapamientos** |  |  |  |  | X |  |  |  |  |  | X | X |  |  |  |
| **RN.04 - Control de aforo** |  |  |  |  |  |  |  |  |  |  | X | X |  | X |  |
| **RN.05 - Condiciones de venta de billetes** |  |  |  |  |  |  |  |  | X | X |  | X |  | X |  |
| **RN.06 - Cálculo del precio del billete** | X | X |  |  |  |  |  |  | X | X |  |  |  | X |  |
| **RN.07 - Anulación automática por cancelación** |  |  |  |  |  |  |  |  |  |  |  | X |  | X |  |
| **RN.08 - Ámbito de actuación del controlador** |  |  |  | X |  |  | X | X |  |  |  | X | X |  | X |
| **RN.09 - Incidencia automática por retraso** |  |  |  | X |  |  |  | X |  |  |  | X | X |  | X |
| **RN.10 - Bloqueo de vehículos averiados** |  |  |  |  |  |  |  |  |  |  | X |  |  |  | X |
| **RN.11 - Compatibilidad de estaciones y líneas** |  |  |  |  |  |  |  | X | X | X |  |  |  |  |  |
| **RN.12 - Validez de los datos de los usuarios** | X | X |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **RN.13 - Validez de los datos de la red y de la flota** |  |  | X |  | X |  | X | X | X |  | X | X |  | X |  |
| **RN.14 - Estructura de las líneas y horario de los viajes** |  |  |  |  |  |  |  | X | X | X |  | X |  |  |  |
| **RN.15 - Registro de los pasos por estación** |  |  |  | X |  |  |  | X |  | X |  | X | X |  |  |
| **RN.16 - Gestión de las incidencias** |  |  |  |  |  |  |  | X |  |  |  | X |  |  | X |
| **RF.01 - Búsqueda de viajes disponibles** |  |  |  |  |  |  |  | X | X | X | X | X |  | X |  |
| **RF.02 - Historial de billetes del cliente** |  | X |  |  |  |  |  |  | X | X |  | X |  | X |  |
| **RF.03 - Agenda del conductor** |  |  |  |  | X |  |  |  | X |  | X | X |  |  |  |
| **RF.04 - Panel de pasos de la central** |  |  |  | X |  |  | X | X | X |  |  | X | X |  |  |
| **RF.05 - Incidencias abiertas de la central** |  |  |  | X |  |  | X | X |  |  | X | X | X |  | X |
| **RF.06 - Ocupación de los viajes de una línea** |  |  |  |  |  |  |  |  | X |  | X | X |  | X |  |
| **RF.07 - Puntualidad por línea** |  |  |  |  |  |  |  |  | X |  |  | X | X |  |  |
| **RF.08 - Ingresos por línea y tarifa** |  |  |  |  |  |  |  |  | X |  |  | X |  | X |  |
| **RF.09 - Vehículos con más averías** |  |  |  |  |  |  |  |  |  |  | X |  |  |  | X |
| **RF.10 - Conductores con licencia próxima a caducar** | X |  | X |  | X |  |  |  |  |  |  | X |  |  |  |

### Matriz de Trazabilidad - Relaciones

| Requisito | Herencia Usuario | Herencia Empleado | Controlador → Central<br>(trabaja en) | Central → Estación<br>(supervisa) | Línea → Parada<br>(compuesta por) | Parada → Estación<br>(se sitúa en) | Administrador → Línea<br>(gestiona) | Viaje → Línea<br>(pertenece a) | Viaje → Vehículo<br>(realizado con) | Viaje → Conductor<br>(conducido por) | PasoEstacion → Viaje<br>(registra paso de) | PasoEstacion → Estación<br>(en) | PasoEstacion → Controlador<br>(anotado por) | Billete → Cliente<br>(comprado por) | Billete → Viaje<br>(para) | Billete → Parada<br>(origen) | Billete → Parada<br>(destino) | Incidencia → Controlador<br>(reportada por) | Incidencia → Viaje<br>(afecta a) | Incidencia → Estación<br>(ocurre en) | Incidencia → Vehículo<br>(implica a) |
|:---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **RI.01** | X | X |  |  |  |  | X |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **RI.02** | X |  |  |  |  |  |  |  |  |  |  |  |  | X |  |  |  |  |  |  |  |
| **RI.03** |  | X | X |  |  |  |  |  |  |  |  |  | X |  |  |  |  | X |  |  |  |
| **RI.04** |  | X |  |  |  |  |  |  |  | X |  |  |  |  |  |  |  |  |  |  |  |
| **RI.05** |  |  | X | X |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **RI.06** |  |  |  | X |  | X |  |  |  |  |  | X |  |  |  |  |  |  |  | X |  |
| **RI.07** |  |  |  |  | X |  | X | X |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **RI.08** |  |  |  |  | X | X |  |  |  |  |  |  |  |  |  | X | X |  |  |  |  |
| **RI.09** |  |  |  |  |  |  |  |  | X |  |  |  |  |  |  |  |  |  |  |  | X |
| **RI.10** |  |  |  |  |  |  |  | X | X | X | X |  |  |  | X |  |  |  | X |  |  |
| **RI.11** |  |  |  |  |  |  |  |  |  |  | X | X | X |  |  |  |  |  |  |  |  |
| **RI.12** |  |  |  |  |  |  |  |  |  |  |  |  |  | X | X | X | X |  |  |  |  |
| **RI.13** |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | X | X | X | X |
| **RN.01** | X |  |  |  |  |  |  | X |  | X |  |  |  |  |  |  |  |  |  |  |  |
| **RN.02** |  |  |  |  |  |  |  | X | X |  |  |  |  |  |  |  |  |  |  |  |  |
| **RN.03** |  |  |  |  |  |  |  |  | X | X |  |  |  |  |  |  |  |  |  |  |  |
| **RN.04** |  |  |  |  |  |  |  |  | X |  |  |  |  |  | X |  |  |  |  |  |  |
| **RN.05** |  |  |  |  | X |  |  | X |  |  |  |  |  |  | X | X | X |  |  |  |  |
| **RN.06** | X |  |  |  |  |  |  |  |  |  |  |  |  | X |  | X | X |  |  |  |  |
| **RN.07** |  |  |  |  |  |  |  |  |  |  |  |  |  |  | X |  |  |  |  |  |  |
| **RN.08** |  |  | X | X |  |  |  |  |  |  |  | X | X |  |  |  |  | X |  | X |  |
| **RN.09** |  |  |  |  |  |  |  |  |  |  | X | X | X |  |  |  |  | X | X | X |  |
| **RN.10** |  |  |  |  |  |  |  |  | X |  |  |  |  |  |  |  |  |  |  |  | X |
| **RN.11** |  |  |  |  | X | X |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **RN.12** | X |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **RN.13** |  | X |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **RN.14** |  |  |  |  | X | X |  | X |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **RN.15** |  |  |  |  | X |  |  | X |  |  | X | X | X |  |  |  |  |  |  |  |  |
| **RN.16** |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | X | X |  |
| **RF.01** |  |  |  |  | X | X |  | X | X |  |  |  |  |  | X |  |  |  |  |  |  |
| **RF.02** |  |  |  |  |  |  |  | X |  |  |  |  |  | X | X | X | X |  |  |  |  |
| **RF.03** |  |  |  |  |  |  |  | X | X | X |  |  |  |  |  |  |  |  |  |  |  |
| **RF.04** |  |  | X | X |  |  |  | X |  |  | X | X |  |  |  |  |  |  |  |  |  |
| **RF.05** |  |  | X | X |  |  |  |  |  |  | X | X |  |  |  |  |  | X | X | X | X |
| **RF.06** |  |  |  |  |  |  |  | X | X |  |  |  |  |  | X |  |  |  |  |  |  |
| **RF.07** |  |  |  |  |  |  |  | X |  |  | X |  |  |  |  |  |  |  |  |  |  |
| **RF.08** |  |  |  |  |  |  |  | X |  |  |  |  |  |  | X |  |  |  |  |  |  |
| **RF.09** |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | X |
| **RF.10** | X | X |  |  |  |  |  |  |  | X |  |  |  |  |  |  |  |  |  |  |  |

### Matriz de Trazabilidad - Restricciones

Las restricciones R.01 a R.16 son las notas del diagrama de restricciones del apartado 5.1. Todas las reglas de negocio (RN.01 a RN.16) quedan recogidas en al menos una restricción.

| Requisito | R.01 | R.02 | R.03 | R.04 | R.05 | R.06 | R.07 | R.08 | R.09 | R.10 | R.11 | R.12 | R.13 | R.14 | R.15 | R.16 |
|:---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **RI.01** |  |  |  |  |  |  |  |  |  |  |  | X |  |  |  |  |
| **RI.02** |  |  |  |  |  | X |  |  |  |  |  | X |  |  |  |  |
| **RI.03** |  |  |  |  |  |  |  | X |  |  |  |  |  |  |  |  |
| **RI.04** | X |  | X |  |  |  |  |  |  |  |  |  | X |  |  |  |
| **RI.05** |  |  |  |  |  |  |  |  |  |  |  |  | X |  |  |  |
| **RI.06** |  |  |  |  |  |  |  |  |  |  | X |  | X |  | X |  |
| **RI.07** |  |  |  |  |  |  |  |  |  |  |  |  | X | X |  |  |
| **RI.08** |  |  |  |  |  |  |  |  |  |  |  |  |  | X |  |  |
| **RI.09** |  | X |  |  |  |  |  |  |  | X |  |  | X |  |  |  |
| **RI.10** |  |  |  |  |  |  |  |  |  |  |  |  | X | X |  |  |
| **RI.11** |  |  |  |  |  |  |  |  |  |  |  |  |  |  | X |  |
| **RI.12** |  |  |  | X |  | X |  |  |  |  |  |  | X |  |  |  |
| **RI.13** |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | X |
| **RN.01** | X |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **RN.02** |  | X |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **RN.03** |  |  | X |  |  |  |  |  |  |  |  |  |  |  |  |  |
| **RN.04** |  |  |  | X |  |  |  |  |  |  |  |  |  |  |  |  |
| **RN.05** |  |  |  |  | X |  |  |  |  |  |  |  |  |  |  |  |
| **RN.06** |  |  |  |  |  | X |  |  |  |  |  |  |  |  |  |  |
| **RN.07** |  |  |  |  |  |  | X |  |  |  |  |  |  |  |  |  |
| **RN.08** |  |  |  |  |  |  |  | X |  |  |  |  |  |  |  |  |
| **RN.09** |  |  |  |  |  |  |  |  | X |  |  |  |  |  |  |  |
| **RN.10** |  |  |  |  |  |  |  |  |  | X |  |  |  |  |  |  |
| **RN.11** |  |  |  |  |  |  |  |  |  |  | X |  |  |  |  |  |
| **RN.12** |  |  |  |  |  |  |  |  |  |  |  | X |  |  |  |  |
| **RN.13** |  |  |  |  |  |  |  |  |  |  |  |  | X |  |  |  |
| **RN.14** |  |  |  |  |  |  |  |  |  |  |  |  |  | X |  |  |
| **RN.15** |  |  |  |  |  |  |  |  |  |  |  |  |  |  | X |  |
| **RN.16** |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | X |
| **RF.01** |  |  |  | X | X |  |  |  |  |  |  |  |  |  |  |  |
| **RF.02** |  |  |  |  |  | X |  |  |  |  |  |  |  |  |  |  |
| **RF.03** |  |  | X |  |  |  | X |  |  |  |  |  |  |  |  |  |
| **RF.04** |  |  |  |  |  |  |  | X | X |  |  |  |  |  | X |  |
| **RF.05** |  |  |  |  |  |  |  | X |  |  |  |  |  |  |  | X |
| **RF.06** |  |  |  | X |  |  | X |  |  |  |  |  |  |  |  |  |
| **RF.07** |  |  |  |  |  |  |  |  | X |  |  |  |  |  | X |  |
| **RF.08** |  |  |  |  |  | X |  |  |  |  |  |  |  |  |  |  |
| **RF.09** |  |  |  |  |  |  |  |  |  | X |  |  |  |  |  |  |
| **RF.10** | X |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |


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

- Norma del proyecto de IISSI-1, curso 2026/27 ([NORMA.md](NORMA.md)).
- Apuntes y transparencias de la asignatura *Introducción a la Ingeniería del Software y los Sistemas de Información I*, Universidad de Sevilla.
- Documentación de PlantUML: diagramas de clases y de objetos ([plantuml.com](https://plantuml.com/es/)).
- PlantText, editor online de PlantUML utilizado para los diagramas ([planttext.com](https://www.planttext.com/)).
- Información pública de horarios y tarifas de transporte interurbano de autobús y de tren, usada como referencia para el dominio.
