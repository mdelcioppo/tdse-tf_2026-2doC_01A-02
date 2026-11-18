
<p align="center">
  <img src="https://www.fi.uba.ar/images/logo-fiuba.png" alt="Logo FIUBA" width="40%">
</p>

# **Sistema Controlador de Horno Gastronómico Eléctrico Inteligente**
## **Informe de Avance**

**Autores:** Matías Del Cioppo, Luciano Andrés Ferrua y Lautaro Ignacio Franco  
**Padrones:** 107453, 109609, 112572  
**Fecha:** 2do cuatrimestre 2026 - 20/11/2026  
**Curso:** Curso 1  
**Grupo:** Grupo 02  

---

## 1. Introducción

En este documento se detalla el estado de avance del Trabajo Final respecto de los requisitos definidos en la sección 2 de [PROPUESTA.md](PROPUESTA.md) (Tabla 2). Cada requisito se marca con uno de los estados de la Tabla 1.

<p align="center"><em>Tabla 1: Referencia de estados de avance.</em></p>

| Estado | Descripción |
| :---: | :--- |
| 🟢 | Implementado y probado. |
| 🟡 | En proceso de implementación. |
| 🔴 | No implementado aún, o no se implementará (se aclara en observaciones). |

---

## 2. Estado de avance de los requisitos

#### **Sensores**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 1.1 | El sistema medirá periódicamente la temperatura interna del horno mediante un sensor térmico (NTC) conectado al ADC o SPI del STM32. | 🟢 | |
| 1.2 | El sistema utilizará un potenciómetro analógico (ADC) para el ajuste fino de intensidad de luz interna. | 🟡 | Finalmente se utilizará solo para luz, no parámetros numéricos.|

#### **Interfaz LCD**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 2.1 | El sistema contará con una pantalla LCD para visualización del menú navegable. | 🟢 | |
| 2.2 | El sistema mostrará en el LCD una pantalla de bienvenida y estado en reposo (`IDLE`) al encender. | 🟢 | |
| 2.3 | El sistema mostrará en el LCD la temperatura actual medida y la temperatura objetivo durante el proceso de cocción. | 🟢 | |
| 2.4 | El sistema mostrará en el LCD el tiempo restante del temporizador de cocción. | 🟡 | |
| 2.5 | El sistema mostrará un mensaje de alerta explícito en el LCD cuando ocurra una sobretemperatura o falla. | 🟡 | |

#### **Entradas / Controles**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 3.1 | El sistema contará con un teclado matricial/botones (`Enter`, `Next`, `Escape`) para la navegación del menú y confirmación de valores. | 🟢 | |
| 3.2 | El sistema implementará filtrado de antirrebote (*debounce*) por software para todos los botones de entrada. | 🟢 | |
| 3.3 | El sistema contará con un bloque de Dip Switches para la selección directa e instantánea de la fuente de calor (Superior, Inferior, Ambas, Convección). | 🟢 | |

#### **Indicadores luminosos**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 4.1 | El sistema contará con un LED indicador para señalar el estado de encendido/reposo (`IDLE`). | 🟢 | |
| 4.2 | El sistema contará con un LED indicador para señalar que las resistencias de calentamiento están activas (`HEATING`). | 🟢 | |
| 4.3 | El sistema contará con un LED indicador para señalar que el horno alcanzó la temperatura objetivo (`SETPOINT OK`). | 🟢 | |
| 4.4 | El sistema notificará condiciones de alarma o falla de seguridad mediante el titileo intermitente de los LEDs indicadores. | 🟡 | |

#### **Actuadores y potencia**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 5.1 | El sistema controlará las resistencias de calentamiento (Superior/Inferior) mediante salidas digitales (GPIO) hacia relés/MOSFETs. | 🔴 |Al calcular la corriente máxima soportada por la placa, entendimos que la implementación de réles no era posible. Se decidió reemplazarlos por leds que den indicación de que vela esta encendida a cada momento, que es más acorde a lo aprendido en la materia y al alcance de la placa.|
| 5.2 | El sistema aplicará modulación por ancho de pulso (PWM) para la regulación dimérica de la luz interna del horno. | 🟡 | |

#### **Buzzer**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 6.1 | El sistema emitirá una melodía corta con el Buzzer al confirmar la selección de un menú o inicio de cocción. | 🟡 | |
| 6.2 | El sistema emitirá un patrón sonoro repetitivo con el Buzzer al finalizar el tiempo del temporizador de cocción. | 🟢 | |
| 6.3 | El sistema emitirá un tono continuo de advertencia acústica cuando se detecte sobretemperatura o estado de FALLA. | 🟡 | |

#### **Comunicación BLE**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 7.1 | El sistema se comunicará mediante el módulo HM-10 (UART) con la aplicación móvil. | 🟢 | |
| 7.2 | El sistema enviará periódicamente a la App la lectura de temperatura interna y el estado del temporizador. | 🟢 | |
| 7.3 | El sistema podrá recibir desde la App comandos de inicio, pausa y parada de emergencia de la cocción. | 🟡 | |
| 7.4 | El sistema podrá recibir desde la App perfiles o parámetros de recetas predefinidas. | 🟢 | |
| 7.5 | El sistema notificará a la App cuando se produzca una alarma de sobretemperatura o cancelación. | 🟢 | |

#### **Memoria (EEPROM / Flash)**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 8.1 | El sistema almacenará la configuración de recetas de usuario en memoria Flash interna. | 🟢 |Se eligió utilizar la Flash Interna |
| 8.2 | El sistema recuperará la última configuración válida almacenada en la Flash durante la secuencia de inicio de firmware. | 🟡 | |

#### **Arquitectura de firmware**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 9.1 | El firmware se estructurará de forma modular (*Bare Metal*) bajo el patrón *Super-Loop* (escuchar/procesar/actuar). | 🟢 | |
| 9.2 | El sistema utilizará una base de tiempo estricta (*System Tick* de 1 ms) para la ejecución no bloqueante de tareas temporizadas. | 🟢 | |
| 9.3 | La lógica de control principal se organizará mediante máquinas de estados finitos (*FSM*). | 🟢 | |

## 3. Dificultades encontradas y ajustes de alcance

Para lograr evaluar cada módulo a implementar se conectaron todos los periféricos en un protoboard y a la STM32, para poder evaluar el funcionamiento y los cambios necesarios, previo a soldar en una placa experimental. En este momento surgió la primera dificultad, debido a que se calcularon las corrientes entrantes y salientes a la placa y se determinó la imposibilidad de implementar los Reles.
Como solución, se decidió colocar Leds indicadores para cada una de las velas y su encendido.
En segundo lugar, se presentó la posibilidad de unificar los leds indicadores en un solo módulo LED que ya había sido usado en TPs anteriores y se decidió eso para reutilizar el código para simplificar la resolución.

## 4. Próximos pasos

En primer lugar se terminará el código para la prueba de cada uno de los módulos y una vez verificado el funcionamiento del mismo y todos los periféricos.
Se diseñara un PCB para poder armar el prototipo de manera estable y según los requisitos del proyecto, además se continuará buscando mejoras posibles para que el MVP (mínimo producto viable).
Finalmente, se intentará finalizar el proyecto para ser presentado en las próximas fechas de final.
