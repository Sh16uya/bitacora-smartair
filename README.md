# Bitácora Semana 13 - Equipo SMAIRT

**Desafío 06 – Sostenibilidad | Monitoreo y gestión de calidad del aire en Lab881**  
*Proyecto Capstone Intermedio 2026 · Universidad Mayor*  
*Contraparte: Municipalidad de Providencia / Hub Providencia · Lab881*

#### MINUTA DE LA 13.ª REUNIÓN
| Campo | Detalle |
| :--- | :--- |
| **Fecha** | 19 / 11 / 2026 |
| **Hora** | 14:00 |
| **Lugar / modalidad** | Presencial (Sala de clases / Laboratorio — Unidad 3, Clase 14) |
| **Integrantes presentes** | Matías Campos, Vanessa Contreras, Maximiliano Castro, Renato Bastías |
| **Integrantes ausentes** | Ninguno |
| **Moderador / Secretario** | Matías Campos / Vanessa Contreras |

---

## 1. Objetivo de la semana
Consolidar el **Segundo Avance** del sistema **AIR881** (Clase 14), presentando los datos de funcionamiento tras integrar los sensores de material particulado y gases, registrando los problemas detectados en la calibración y organizando el plan técnico para el Tercer Avance (26 de noviembre) y la Feria Capstone (3 de diciembre).

## 2. Revisión de compromisos de la semana anterior
| Tarea | Responsable | Estado | Observaciones |
| :--- | :--- | :--- | :--- |
| Adquisición e integración física de sensores DSM501A (PM2.5) y MQ-135 (COV) | Renato Bastías / Maximiliano Castro | Cumplida con observaciones | Sensores integrados al circuito del ESP32; el MQ-135 entrega señal activa pero aún no está bien calibrado. |
| Actualización del código con lectura de MQ-135, DSM501A y visualización en LCD | Matías Campos | Cumplida | Código operativo sin bloqueos (`millis()`), mostrando temperatura, humedad, MQ, DSM y estado de silencio. |
| Diseño inicial de estructura para Póster A0 y soporte visual del stand | Vanessa Contreras | En progreso | Estructura base definida según pauta de la Feria Capstone para su cierre previo al 26 de noviembre. |
| Documentación de pruebas de banco y registro de problemas encontrados | Todo el equipo | Cumplida | Levantamiento de fallas y limitaciones de calibración presentado en tutoría de 10 minutos. |

## 3. Desarrollo del trabajo

### 3.1 Integración de Sensores de Calidad del Aire y Validación en Banco (Segundo Avance)
Completamos la arquitectura física del sistema **AIR881** sumando los módulos específicos de contaminación al circuito base (ESP32, DHT11, RTC DS3231, LCD 1602 I2C, semáforo LED, buzzer, botón de silencio y potenciómetro).
* **Hallazgos:** Las pruebas de banco confirmaron la estabilidad del código sin bloqueos mediante temporizadores (`millis()`), la respuesta inmediata del botón de silencio (GPIO 32) y la lectura activa del módulo MQ-135 (GPIO 35) y del sensor de partículas DSM501A (GPIO 33).
* **Dificultades y problemas detectados:**
  * **Falta de calibración fina en el sensor MQ-135:** Aunque el sensor ya responde a cambios ambientales, actualmente entrega lecturas analógicas base muy bajas (oscilando en un rango de `0–20` hasta `~40` sobre `4095` en ambiente limpio). Esto se debe a que el elemento semiconductor aún no cuenta con una calibración formal de su resistencia de carga (RL), requiere un periodo prolongado de precalentamiento (*burn-in*) y exige ajustar los umbrales de software a su curva real de respuesta.
  * **Acondicionamiento de señal (5V a 3.3V):** Se incorporaron resistencias en serie de 330 ohmios en las líneas de señal del MQ-135 y DSM501A para proteger las entradas lógicas de 3.3V del ESP32, dejando identificada la necesidad de revisar estabilidad de contactos en protoboard.
* **Decisiones tomadas:** Utilizar valores relativos respecto a una línea base medida en ambiente limpio (cortadora apagada), mantener el potenciómetro (GPIO 34) como herramienta de simulación controlada de peaks durante las pruebas y demostraciones, y priorizar el ajuste de umbrales del MQ-135 durante la próxima semana hacia el Tercer Avance.

### 3.2 Tutoría de Avance y Organización hacia la Feria Capstone (3 de diciembre)
Durante la sesión de tutoría de 10 minutos revisamos el protocolo de pruebas, los datos obtenidos, los problemas detectados y la organización de roles para la feria (*Ver, Probar y Explicar*).
* **Hallazgos:** Para la Feria Capstone del 3 de diciembre (14:00 a 16:30 h en Campus Manuel Montt) se requieren **3 integrantes atendiendo el stand y 2 presentando el pitch de 5 minutos**, además de contar con un Plan B formal ante fallos técnicos en vivo.
* **Decisiones tomadas:** Asignar la exposición del pitch a **Matías Campos y Vanessa Contreras**, mientras **Maximiliano Castro y Renato Bastías** lideran la demostración interactiva y atención técnica del stand (rotando para cumplir con la dotación de 3 personas en mesa). Se acordó respaldar el prototipo con videos grabados del funcionamiento, capturas del Monitor Serie y el póster A0 impreso.

## 4. Validación: Certezas e Incógnitas
**Lo que sabemos (Certezas):**
* El prototipo **AIR881** opera de forma autónoma leyendo variables ambientales (temperatura, humedad, pulso de partículas y señal analógica de gases), mostrándolas en tiempo real en pantalla LCD y ejecutando alertas visuales y sonoras según umbrales definidos.
* El potenciómetro permite simular de forma gradual y controlada el aumento de contaminantes para validar la transición del semáforo (verde, amarillo, rojo) y el silenciado del buzzer.

**Lo que necesitamos validar / resolver para el Tercer Avance (26 de noviembre):**
* **Calibración y estabilización del MQ-135:** Realizar un precalentamiento continuo para fijar su línea base real y ajustar en el código los umbrales de activación de alerta amarilla y roja para que correspondan al rango efectivo del sensor.
* **Pruebas de repetibilidad del DSM501A:** Registrar respuesta ante generación controlada de partículas para documentar indicadores concretos de tiempo de reacción.

## 5. Avance del prototipo
| Componente | Estado | Detalle |
| :--- | :--- | :--- |
| **Sensor DHT11 (Temp/Hum)** | Listo | Operativo en GPIO 4 con lecturas estables cada 2 segundos. |
| **Sensor DSM501A (PM2.5)** | En prueba | Integrado en GPIO 33 (con resistencia de protección de 330 ohmios) detectando pulsos de partículas. |
| **Sensor MQ-135 (COV/Gases)** | En ajuste | Integrado en GPIO 35 (con resistencia de 330 ohmios); responde al ambiente (~0–40 en reposo), pero **aún no está bien calibrado** para medir concentraciones exactas. |
| **Pantalla y Reloj (LCD + RTC)** | Listo | Bus I2C (GPIO 21/22) mostrando Temperatura, Humedad, MQ, DSM y estado de alarma (Silenciado/Activo). |
| **Sistema de alertas y simulación** | Listo | Semáforo LED, buzzer activo con botón de silencio (GPIO 32) y potenciómetro (GPIO 34) para simular peaks. |
| **Soporte visual y difusión** | En progreso | Estructura del Póster A0 definida y guion de pitch de 5 minutos en preparación. |

## 6. Acuerdos y tareas para la próxima semana (hacia el Tercer Avance - 26 nov)
| Tarea | Responsable | Fecha límite |
| :--- | :--- | :--- |
| Precalentamiento, pruebas de respuesta y ajuste de umbrales de línea base del sensor MQ-135 en el código | Matías Campos / Renato Bastías | 25 / 11 / 2026 |
| Finalización del diseño gráfico del Póster A0 vertical con fotos comparativas de ambos prototipos | Vanessa Contreras | 25 / 11 / 2026 |
| Consolidación de mediciones, tabla de pruebas y actualización del repositorio de GitHub | Maximiliano Castro / Matías Campos | 26 / 11 / 2026 |
| Presentación de Tercer Avance con prototipo operativo y documentación completa | Todo el equipo | 26 / 11 / 2026 |
| Ensayo general de Pitch (5 min) en campus y montaje en Feria Capstone | Todo el equipo | 03 / 12 / 2026 |

## 7. Riesgos y apoyo requerido
* **Riesgos:** Falta de sensibilidad o lecturas erráticas del sensor MQ-135 por calibración incompleta o tiempo insuficiente de calentamiento; falso contacto en cables jumper de la protoboard durante el traslado.
* **Mitigación / Apoyo requerido:** Encender el prototipo con antelación previa a las mediciones y presentaciones para estabilizar el calefactor interno del MQ-135, reforzar el cableado en protoboard/shield y mantener el potenciómetro operativo como respaldo de demostración.

## 8. Evidencias 
