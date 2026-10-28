
<p align="center">
  <img src="https://www.fi.uba.ar/images/logo-fiuba.png" alt="Logo FIUBA" width="40%">
</p>

# **Sistema Controlador de Horno Gastronómico Eléctrico Inteligente**
## **Informe de Avance**

**Autores:** Matías Del Cioppo, Luciano Andrés Ferrua y Lautaro Ignacio Franco  
**Padrones:** 107453, 109609, 112572  
**Fecha:** 2do cuatrimestre 2026 - DD/MM/2026  
**Curso:** Curso 1  
**Grupo:** Grupo 02  

---

## 1. Introducción

En este documento se detalla el estado de avance del Trabajo Final respecto de los requisitos definidos en la sección 2 de [PROPUESTA.md](PROPUESTA.md) (Tabla 2.1). Cada requisito se marca con uno de los estados de la Tabla 1.1.

<p align="center"><em>Tabla 1.1: Referencia de estados de avance.</em></p>

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
| 1.1 | El sistema medirá periódicamente la temperatura interna del horno mediante un sensor térmico (NTC / Termocupla) conectado al ADC o SPI del STM32. | 🔴 | |
| 1.2 | El sistema utilizará un potenciómetro analógico (ADC) para el ajuste fino de intensidad de luz interna o de parámetros numéricos. | 🔴 | |

#### **Interfaz LCD**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 2.1 | El sistema contará con una pantalla LCD (2x16 / 20x4) para visualización del menú navegable. | 🔴 | |
| 2.2 | El sistema mostrará en el LCD una pantalla de bienvenida y estado en reposo (`IDLE`) al encender. | 🔴 | |
| 2.3 | El sistema mostrará en el LCD la temperatura actual medida y la temperatura objetivo durante el proceso de cocción. | 🔴 | |
| 2.4 | El sistema mostrará en el LCD el tiempo restante del temporizador de cocción. | 🔴 | |
| 2.5 | El sistema mostrará un mensaje de alerta explícito en el LCD cuando ocurra una sobretemperatura o falla. | 🔴 | |

#### **Entradas / Controles**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 3.1 | El sistema contará con un teclado matricial/botones (`Enter`, `Next`, `Escape`) para la navegación del menú y confirmación de valores. | 🔴 | |
| 3.2 | El sistema implementará filtrado de antirrebote (*debounce*) por software para todos los botones de entrada. | 🔴 | |
| 3.3 | El sistema contará con un bloque de Dip Switches para la selección directa e instantánea de la fuente de calor (Superior, Inferior, Ambas, Convección). | 🔴 | |

#### **Indicadores luminosos**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 4.1 | El sistema contará con un LED indicador para señalar el estado de encendido/reposo (`IDLE`). | 🔴 | |
| 4.2 | El sistema contará con un LED indicador para señalar que las resistencias de calentamiento están activas (`HEATING`). | 🔴 | |
| 4.3 | El sistema contará con un LED indicador para señalar que el horno alcanzó la temperatura objetivo (`SETPOINT OK`). | 🔴 | |
| 4.4 | El sistema notificará condiciones de alarma o falla de seguridad mediante el titileo intermitente de los LEDs indicadores. | 🔴 | |

#### **Actuadores y potencia**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 5.1 | El sistema controlará las resistencias de calentamiento (Superior/Inferior) mediante salidas digitales (GPIO) hacia relés/MOSFETs. | 🔴 | |
| 5.2 | El sistema aplicará modulación por ancho de pulso (PWM) para la regulación dimérica de la luz interna del horno. | 🔴 | |

#### **Buzzer**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 6.1 | El sistema emitirá una melodía corta con el Buzzer al confirmar la selección de un menú o inicio de cocción. | 🔴 | |
| 6.2 | El sistema emitirá un patrón sonoro repetitivo con el Buzzer al finalizar el tiempo del temporizador de cocción. | 🔴 | |
| 6.3 | El sistema emitirá un tono continuo de advertencia acústica cuando se detecte sobretemperatura o estado de FALLA. | 🔴 | |

#### **Comunicación BLE**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 7.1 | El sistema se comunicará mediante el módulo HM-10 (UART) con la aplicación móvil. | 🔴 | |
| 7.2 | El sistema enviará periódicamente a la App la lectura de temperatura interna y el estado del temporizador. | 🔴 | |
| 7.3 | El sistema podrá recibir desde la App comandos de inicio, pausa y parada de emergencia de la cocción. | 🔴 | |
| 7.4 | El sistema podrá recibir desde la App perfiles o parámetros de recetas predefinidas. | 🔴 | |
| 7.5 | El sistema notificará a la App cuando se produzca una alarma de sobretemperatura o cancelación. | 🔴 | |

#### **Memoria (EEPROM / Flash)**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 8.1 | El sistema almacenará la configuración de recetas de usuario en memoria EEPROM externa (vía bus I2C) o Flash interna. | 🔴 | |
| 8.2 | El sistema recuperará la última configuración válida almacenada en la EEPROM durante la secuencia de inicio de firmware. | 🔴 | |

#### **Arquitectura de firmware**

| Req ID | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 9.1 | El firmware se estructurará de forma modular (*Bare Metal*) bajo el patrón *Super-Loop* (escuchar/procesar/actuar). | 🔴 | |
| 9.2 | El sistema utilizará una base de tiempo estricta (*System Tick* de 1 ms) para la ejecución no bloqueante de tareas temporizadas. | 🔴 | |
| 9.3 | La lógica de control principal se organizará mediante máquinas de estados finitos (*FSM*). | 🔴 | |

---

## 3. Estado de los casos de uso

<p align="center"><em>Tabla 3.1: Estado de avance de los casos de uso definidos en la propuesta.</em></p>

| Caso de uso | Descripción | Estado | Observaciones |
| :---: | :--- | :---: | :--- |
| 1 | Configuración e inicio de cocción manual desde la interfaz local. | 🔴 | |
| 2 | Control y monitoreo remoto vía Bluetooth (App). | 🔴 | |
| 3 | Interrupción remota de emergencia del proceso de cocción. | 🔴 | |

---

## 4. Dificultades encontradas y ajustes de alcance

*(Completar)*

## 5. Próximos pasos

*(Completar)*
