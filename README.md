# Bitácora Semana 3 - Equipo SMAIRT

Bienvenido a la documentación de la tercera semana de trabajo en el marco del **Desafío 06: Monitoreo y gestión de calidad del aire en Lab881**. 

![Equipo SMAIRT](image_3.png)

---

## 1. Pitch: El Enemigo Invisible en Lab881

**1. El Gancho (Contexto)**
¿Se imaginan trabajar todos los días creando e innovando, pero respirando un aire que pone en riesgo su salud sin que ustedes lo noten? Esta es la realidad actual de los operadores en el espacio de fabricación Lab881 del Hub Providencia. 

**2. El Problema (Causa)**
Enmarcado en el área de Sostenibilidad, nos enfrentamos a un problema crítico: la máquina de corte y grabado láser del taller genera constantemente material particulado y gases contaminantes. El sistema de filtrado con el que cuenta esta máquina actualmente es insuficiente para contener estas emisiones.

**3. El Impacto (Consecuencia)**
Esto genera un deterioro constante de la calidad del aire. Hoy en día, los operadores intentan aplicar medidas de ventilación en el taller, pero lo hacen "a ciegas" porque no cuentan con herramientas para medir la calidad del aire ni tienen información objetiva. Esta falta de datos les impide saber en qué momento exacto el ambiente se vuelve realmente peligroso, dejándolos expuestos a diario. 

**4. El Desafío (Cierre)**
Nuestro Desafío 06 busca visibilizar este problema invisible. El reto de nuestro equipo no es solo tecnológico, es de salud humana: necesitamos encontrar la manera de gestionar el estado del aire entregando información en tiempo real. Solo así podremos orientar de manera eficiente las medidas de ventilación y proteger la salud de quienes dan vida al espacio Lab881.

---

## 2. Definición y Alcance del Desafío

* **Tema o ámbito del desafío:** Aborda específicamente la necesidad de monitoreo y gestión de la calidad del aire frente a emisiones contaminantes.
* **Contexto:** La situación ocurre dentro del espacio de fabricación Lab881, correspondiente al Hub Providencia.
* **Actores involucrados o afectados:** Los principales afectados son los operadores del espacio de fabricación Lab881 que trabajan a diario con la maquinaria.
* **Equipo ejecutor (SMAIRT):** Matías Campos, Vanessa Contreras, Maximiliano Castro y Renato Bastías.
* **Situación problemática inicial:** La máquina de corte y grabado láser genera constantemente material particulado y gases contaminantes durante su operación, y el sistema de filtrado actual es ineficiente e insuficiente para el espacio.
* **Relevancia del problema:** Existe un deterioro constante del aire que representa un riesgo inminente y a largo plazo para la salud de los operadores. Dado que basan su percepción de riesgo en la subjetividad y no saben en qué momento exacto el ambiente se vuelve peligroso, no pueden tomar medidas de mitigación o ventilación a tiempo.
* **Qué sabemos hasta ahora:** El filtro de la máquina no da abasto. Tenemos la certeza de que la solución obligatoriamente requiere un sistema de monitoreo continuo compuesto por sensores en tiempo real y un sistema de alertas para activar la ventilación.
* **Qué todavía no saben:** Faltan definir las opciones técnicas viables de sensores específicos para medir compuestos orgánicos volátiles (VOCs) y material particulado. También necesitamos investigar los límites permisibles exactos según la normativa chilena de calidad del aire para fijar los umbrales de alerta, estructurar la arquitectura de comunicación entre los sensores y la pantalla, y diseñar el diagrama de flujo lógico para emitir las alertas oportunas.

---

## 3. Ficha Diagnóstica: ¿Dónde estamos?

### 1. ¿Qué está ocurriendo? (El problema observable)
La máquina de corte y grabado láser del taller genera gases contaminantes y material particulado todo el tiempo, y el filtro que tiene ahora simplemente no da abasto para limpiar el ambiente.

### 2. ¿A quién le ocurre?
Directamente a las operadoras y operadores que trabajan en el espacio de fabricación Lab881.

### 3. ¿Por qué importa? (3 consecuencias concretas)
* El aire del espacio se deteriora constantemente mientras la máquina funciona.
* Existe un riesgo directo e inminente para la salud respiratoria del equipo.
* Como no hay datos reales, la gente termina ventilando "a ciegas", exponiéndose al peligro sin darse cuenta.

### 4. ¿Qué sabemos?
* El problema está radicado en el Lab881 del Hub Providencia.
* La fuente principal es la cortadora láser.
* Tomar medidas reactivas o guiarse por la intuición no sirve de nada.
* La solución que armemos necesita sí o sí sensores y un sistema de alertas.

### 5. ¿Qué creemos pero aún no sabemos? (Supuestos)
* Creemos que los operadores sienten una falsa seguridad; si no ven humo o no huelen nada raro, asumen que el aire está "bien".
* Asumimos que al contar con datos visibles en tiempo real, los usuarios modificarán de inmediato sus prácticas de ventilación y cuidado.

### 6. ¿Qué necesitan averiguar y todavía no saben? (4 preguntas prioritarias)
1. ¿Qué sensores específicos y reales nos sirven para medir los VOCs y el material particulado?
2. ¿Cuáles son los límites permisibles exactos según la normativa chilena para saber cuándo hacer que salte la alerta?
3. ¿Cómo estructuramos la arquitectura técnica para que los sensores den el aviso en tiempo real?
4. ¿Cómo armamos la lógica y el diagrama de flujo para que las alarmas (visuales o sonoras) avisen justo en el momento oportuno?

---

## 4. Próximos Pasos (Investigación y Desarrollo)

* **Hardware y Sensores:** Selección comparativa de módulos para medición de material particulado (PM2.5 / PM10) y sensores de VOCs.
* **Marco Regulatorio:** Revisión del Decreto Supremo 594 sobre condiciones ambientales en lugares de trabajo para parametrizar los umbrales de riesgo.
* **Arquitectura del Sistema:** Definición del microcontrolador y los protocolos de comunicación entre los sensores y el módulo de visualización.
* **Lógica de Alertas:** Diagrama de estados para determinar cuándo activar avisos visuales (LED/pantalla) y sonoros (buzzer).
