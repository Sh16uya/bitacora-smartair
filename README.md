# Bitácora Semana 12 - Equipo SMAIRT

**Desafío 06 – Sostenibilidad | Monitoreo y gestión de calidad del aire en Lab881**

Proyecto Capstone Intermedio 2026 · Universidad Mayor
Municipalidad de Providencia / Hub Providencia · Lab881

**Equipo SMAIRT:** Matías Campos, Vanessa Contreras, Maximiliano Castro y Renato Bastías.

---

## Proyecto: AIR881

**Monitoreo inteligente de la calidad del aire en LAB881**

### Problema

La operación de la cortadora y grabadora láser del LAB881 puede generar material particulado, compuestos orgánicos volátiles (COV) y otros contaminantes. Existe un sistema de filtrado que la contraparte considera insuficiente, por lo que se requiere información en tiempo real para evaluar las condiciones del espacio y orientar medidas de ventilación y seguridad.

### Solución

AIR881 es un sistema piloto de monitoreo continuo que mide variables ambientales, las visualiza en una pantalla y genera alertas visuales y sonoras cuando se superan umbrales previamente definidos.

```
Corte láser → Sensores → Visualización → Alertas → Acción
```

---

## 1. ¿Qué hicimos? ¿Cómo vamos?

### Resultados

Armamos el prototipo físico de AIR881 con:

- ESP32 DevKit V1 con shield de expansión
- Pantalla LCD 1602 I2C
- Sensor DHT11 (temperatura y humedad)
- Reloj RTC DS3231
- Semáforo de LEDs (verde, amarillo y rojo), más un LED rojo principal
- Buzzer activo (más un buzzer principal)
- Botón (silenciar alarma) y potenciómetro

### Estado actual

El sistema lee temperatura y humedad, las muestra en pantalla junto con la hora, y activa alertas visuales y sonoras según umbrales de temperatura definidos.

### Lo que nos falta

Incorporar sensores de calidad del aire, que son el núcleo del desafío:

| Sensor | Qué mediría | Precio (CLP) |
|---|---|---|
| **DSM501A** | Partículas (PM2.5), principal contaminante del humo del láser | $9.990 |
| **MQ-135** | Indicador general de COV y gases | $3.400 |
| **Total** | | **$13.390** |

La arquitectura ya está lista para recibirlos. Los valores de estos sensores serían **relativos o estimados** (no mediciones certificadas), por lo que las alertas se definirán respecto de una línea base medida con el láser apagado.

Otras mejoras pendientes:

- Envío de datos por WiFi a un dashboard (por ejemplo ThingSpeak).
- Propuesta técnica de mitigación (ventilación/extracción) basada en los datos medidos antes, durante y después del corte láser.

---

## 2. ¿Cómo lo comprobamos?

- **Pruebas:** verificamos la comunicación I2C del LCD y el RTC (pines SDA/SCL), la lectura del DHT11 y el cambio de estado del semáforo y el buzzer al superar los umbrales.
- **Indicadores:** estabilidad de la lectura en pantalla, tiempo de respuesta de las alertas (LEDs y buzzer) y hora de cada lectura mediante el RTC.
- **Fallas superadas:**
  - Errores de compilación por dependencias de librerías.
  - Problemas de contraste en el LCD.
- **Limitación actual:** hoy medimos temperatura y humedad. Las variables de contaminación (partículas y COV) dependen de los sensores pendientes.

---

## Hardware

| Componente | Función |
|---|---|
| ESP32 DevKit V1 + shield de expansión | Microcontrolador |
| LCD 1602 I2C (0x27) | Visualización permanente |
| DHT11 | Temperatura y humedad |
| RTC DS3231 | Hora de cada lectura |
| LEDs verde / amarillo / rojo | Semáforo de alerta visual |
| Buzzer activo | Alerta sonora |
| Botón | Silenciar o reactivar la alarma |
| Potenciómetro 10kΩ | Valor mostrado en pantalla |

### Conexión de pines

| Función | Pin ESP32 |
|---|---|
| I2C SDA (LCD y RTC) | GPIO21 |
| I2C SCL (LCD y RTC) | GPIO22 |
| DHT11 (datos) | GPIO4 |
| LED verde | GPIO25 |
| LED amarillo | GPIO26 |
| LED rojo | GPIO27 |
| LED rojo principal | GPIO19 |
| Buzzer | GPIO14 |
| Buzzer principal | GPIO23 |
| Botón (`INPUT_PULLUP`) | GPIO32 |
| Potenciómetro | GPIO34 |

El LCD y el RTC comparten el bus I2C (direcciones 0x27 y 0x68).

---

## Lógica de alertas

| Estado | Condición | Acción |
|---|---|---|
| 🟢 Verde | Temperatura < 30 °C | Normal |
| 🟡 Amarillo | 30 °C ≤ temperatura < 35 °C | LED amarillo encendido |
| 🔴 Rojo | Temperatura ≥ 35 °C | LED rojo y buzzer (silenciable con el botón) |

En pantalla se muestra:

- **Línea 1:** temperatura y humedad.
- **Línea 2:** hora, estado del buzzer (`A` = activo, `S` = silenciado) y valor del potenciómetro.

---

## el código

Código del Sistema (Arduino IDE)

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <DHT.h>
#include <RTClib.h>

// 1. Configuración de la pantalla LCD (I2C: 0x27)
LiquidCrystal_I2C lcd(0x27, 16, 2);

// 2. Configuración del sensor DHT11 (Pin Digital 4)
#define DHTPIN 4     
#define DHTTYPE DHT11 
DHT dht(DHTPIN, DHTTYPE);

// 3. Configuración del módulo RTC DS3231 (I2C)
RTC_DS3231 rtc;

// 4. Configuración de Pines
const int ledVerde = 25;    
const int ledAmarillo = 26; 
const int ledRojo = 27;     
const int ledRojoPrincipal = 19; 
const int buzzer = 14;      
const int buzzerPrincipal = 23;
const int botonPin = 32;    
const int potPin = 34;      

// Umbrales de temperatura
const float UMBRAL_AMARILLO = 30.0; 
const float UMBRAL_ROJO = 35.0;     

// Variables de control
bool buzzerSilenciado = false;
bool estadoAnteriorBoton = HIGH;
unsigned long ultimoTiempoBoton = 0;
unsigned long tiempoUltimaLecturaDHT = 0;
const long intervaloDHT = 2000; 

float t = 0.0;
float h = 0.0;
int valorPotSuavizado = 0; 

void setup() {
  Serial.begin(115200);
  delay(1000);

  dht.begin();
  lcd.init();                      
  lcd.backlight();
  
  if (!rtc.begin()) {
    Serial.println("Advertencia: No se encuentra el módulo RTC DS3231");
  }

  pinMode(ledVerde, OUTPUT);
  pinMode(ledAmarillo, OUTPUT);
  pinMode(ledRojo, OUTPUT);
  pinMode(ledRojoPrincipal, OUTPUT);
  pinMode(buzzer, OUTPUT);
  pinMode(buzzerPrincipal, OUTPUT);
  pinMode(botonPin, INPUT_PULLUP);

  lcd.setCursor(0, 0);
  lcd.print("Sistema AIR881");
  lcd.setCursor(0, 1);
  lcd.print("Iniciando...");
  delay(2000);
  lcd.clear();
}

void loop() {
  // LECTURA DEL BOTÓN (Antirrebote rápido para silenciar/activar)
  bool estadoBotonActual = digitalRead(botonPin);
  if (estadoAnteriorBoton == HIGH && estadoBotonActual == LOW) {
    if (millis() - ultimoTiempoBoton > 250) { 
      buzzerSilenciado = !buzzerSilenciado;   
      ultimoTiempoBoton = millis();
    }
  }
  estadoAnteriorBoton = estadoBotonActual;

  // LECTURA DEL SENSOR DHT11 (Usando millis para no congelar)
  unsigned long tiempoActual = millis();
  if (tiempoActual - tiempoUltimaLecturaDHT >= intervaloDHT) {
    tiempoUltimaLecturaDHT = tiempoActual;
    float tempLeida = dht.readTemperature();
    float humLeida = dht.readHumidity();
    if (!isnan(tempLeida) && !isnan(humLeida)) {
      t = tempLeida;
      h = humLeida;
    }
  }

  // LECTURA Y SUAVIZADO DEL POTENCIÓMETRO (De 0 a 999 de forma gradual)
  int lecturaBruta = analogRead(potPin); 
  int lecturaMapeada = map(lecturaBruta, 0, 4095, 0, 999); 
  valorPotSuavizado = (valorPotSuavizado * 0.8) + (lecturaMapeada * 0.2);

  DateTime now = rtc.now();

  // MOSTRAR DATOS EN LCD
  lcd.setCursor(0, 0);
  lcd.print("T:");
  lcd.print(t);
  lcd.print("C H:");
  lcd.print((int)h);
  lcd.print("%  ");

  lcd.setCursor(0, 1);
  lcd.print("H:");
  if (now.hour() < 10) lcd.print("0");
  lcd.print(now.hour(), DEC);
  lcd.print(':');
  if (now.minute() < 10) lcd.print("0");
  lcd.print(now.minute(), DEC);
  lcd.print(':');
  if (now.second() < 10) lcd.print("0");
  lcd.print(now.second(), DEC);
  
  lcd.print(buzzerSilenciado ? " S:" : " A:");
  lcd.print(valorPotSuavizado);
  lcd.print("   ");

  // LÓGICA DE SEMÁFORO Y ALERTAS
  if (t >= UMBRAL_ROJO) {
    digitalWrite(ledRojo, HIGH);
    digitalWrite(ledRojoPrincipal, HIGH);
    digitalWrite(ledAmarillo, LOW);
    digitalWrite(ledVerde, LOW);
    
    if (!buzzerSilenciado) {
      digitalWrite(buzzer, HIGH);
      digitalWrite(buzzerPrincipal, HIGH);
    } else {
      digitalWrite(buzzer, LOW);
      digitalWrite(buzzerPrincipal, LOW);
    }
    
  } else if (t >= UMBRAL_AMARILLO) {
    digitalWrite(ledAmarillo, HIGH);
    digitalWrite(ledRojo, LOW);
    digitalWrite(ledRojoPrincipal, LOW);
    digitalWrite(ledVerde, LOW);
    digitalWrite(buzzer, LOW);
    digitalWrite(buzzerPrincipal, LOW);
  } else {
    digitalWrite(ledVerde, HIGH);
    digitalWrite(ledAmarillo, LOW);
    digitalWrite(ledRojo, LOW);
    digitalWrite(ledRojoPrincipal, LOW);
    digitalWrite(buzzer, LOW);
    digitalWrite(buzzerPrincipal, LOW);
  }
}

```

## 3. ¿Quién hará qué?

| Rol | Integrantes |
|---|---|
|  |  |
| | |

Tareas técnicas (circuito, código, material gráfico): _(completar con la persona a cargo de cada una)_.

---

## 4. ¿Cómo será el stand?

- **Prototipo:** AIR881 funcionando en vivo sobre protoboard y shield, con la pantalla visible.
- **Interacción:** el evaluador puede usar el botón para silenciar la alarma y ver cómo cambia el semáforo ante variaciones de temperatura.
- **Soporte visual:** infografía de AIR881 y mapa de empatía del LAB881 para explicar el problema del "enemigo invisible".
- **Plan B:** fotos del montaje, capturas del código y un video corto del funcionamiento previo.

---

##Archivos.
!('WhatsApp Image 2026-11-12 at 16.53.41.jpeg')
