<p align="center">
  <img src="https://www.fi.uba.ar/images/logo-fiuba.png" alt="Logo FIUBA" width="40%">
</p>

# **Sistema Controlador de Horno Gastronómico Eléctrico Inteligente**

**Autores:** Matías Del Cioppo, Luciano Andrés Ferrua y Lautaro Ignacio Franco  
**Padrones:** 107453, 109609, 112572  
**Fecha:** 2do cuatrimestre 2026 - 30/10/2026  
**Curso:** Curso 1    
**Grupo:** Grupo 02  
---

## 1. Selección del proyecto a implementar

### 1.1 Objetivo del proyecto y resultados esperados
El objetivo principal de este proyecto es diseñar e implementar un **controlador de horno gastronómico eléctrico inteligente** basado en la placa de desarrollo **STM32 NUCLEO-F103RB**. El sistema permitirá gestionar el proceso de cocción mediante la selección de modos de operación, control preciso de temperatura, temporización y ajuste del sentido/fuente de calor (superior, inferior, ambos, etc).

Adicionalmente, el sistema ofrecerá:
* **Conectividad remota vía Bluetooth (módulo HM-10):** Monitoreo y configuración de recetas o parámetros desde una aplicación móvil.
* **Interfaz de usuario local e interactiva:** Basada en pantalla LCD 2x16/20x4, teclado matricial de entrada, Dip Switches de selección rápida y potenciómetro para ajuste de intensidad de luz interna o regulación fina de temperatura.
* **Seguridad y señalización:** Medición de temperatura en tiempo real mediante sensor térmico (ADC/NTC o MAX6675/K-Type), sistema de alarmas acústicas mediante Buzzer (y alertas mediante Bluetooth) y luces indicadoras de estado.
* **Almacenamiento persistente:** Guardado de perfiles de cocción (recetas) y configuraciones en memoria Flash interna o EEPROM externa I2C.

Este prototipo busca proporcionar un Producto Mínimo Viable (MVP) modular y adaptable a electrodomésticos comerciales (como hornos Gadnic, TopHouse, etc.), priorizando la precisión técnica, la seguridad operativa y el uso eficiente de los recursos del microcontrolador. Por último, se busca en este proyecto poder presentar el controlador diseñado cómo un modelo comerciable.

---

### 1.2 Proyectos similares

Basados en nuestros conocimientos previos e investigación sobre el desarrollo que deseamos implementar, se evaluaron tres alternativas de prototipos para el control térmico de cocción:

1. **Controlador Horno Gastronómico Inteligente Completo (STM32):** Control de temperatura con sensor en tiempo real, menú interactivo en LCD, conectividad Bluetooth (HM-10), dimming/luces, teclado, Dip Switches, alertas sonoras con Buzzer y almacenamiento de recetas/modos de cocción en Flash/EEPROM.
2. **Controlador Básico de Horno (STM32):** Control de temperatura ON/OFF mediante potenciómetro, lectura analógica sin menú interactivo ni conectividad remota.
3. **Temporizador / Termostato genérico (Arduino UNO):** Control básico temporizado sin arquitectura no bloqueante ni integración con componentes embebidos de grado industrial.

Para la selección del proyecto se establecieron 6 criterios característicos ponderados del 1 al 10:

* **Disponibilidad del hardware (Peso: 10):** Componentes obtenibles localmente en el mercado regional.
* **Calidad de la experiencia de usuario (Peso: 8):** Riqueza del menú local, interfaz remota por App y alertas.
* **Escalabilidad, adaptabilidad y flexibilidad (Peso: 8):** Capacidad de agregar nuevos modos de cocción o integrar periféricos por buses estándar (I2C/SPI/UART) y/o poder ser acoplable a tecnología existente.
* **Dificultad técnica / Viabilidad (Peso: 9):** Integración con la arquitectura de la materia (*Bare Metal*, *Event-Triggered System*, *Super-Loop* sin bloqueos).
* **Costo (Peso: 5):** Accesibilidad económica de los módulos.
* **Interés personal del grupo (Peso: 8):** Motivación por aplicar los conocimientos del curso en un electrodoméstico de uso cotidiano.

#### **Tabla 1.2.1: Comparación de alternativas de proyectos**
Para la selección del proyecto se utilizaron los criterios numerados previamente y se evaluaron de la siguiente manera:
| Proyectos | Controlador Horno Gastronómico Inteligente STM32 | Controlador Básico STM32 | Termostato Básico Arduino |
| :--- | :---: | :---: | :---: |
| **Criterio**| **Puntaje \| P. Ponderado** | **Puntaje \| P. Ponderado** | **Puntaje \| P. Ponderado** |
| Disponibilidad de Hardware (peso: 10) | 10 \| 100 | 10 \| 100 | 10 \| 100 |
| Calidad de Experiencia de Usuario (peso: 8) | 10 \| 80 | 5 \| 40 | 4 \| 32 |
| Escalabilidad, Adaptabilidad  y Flexibilidad (peso: 8) | 9 \| 72 | 5 \| 40 | 3 \| 24 |
| Dificultad Técnica / Viabilidad (peso: 9) | 8 \| 72 | 9 \| 81 | 9 \| 81 |
| Costo (peso: 5) | 7 \| 35 | 9 \| 45 | 9 \| 45 |
| Interés Personal (peso: 8) | 10 \| 80 | 6 \| 48 | 4 \| 32 |
| **Puntaje Total** | **439** | **354** | **314** |
<p align="center"><em>Tabla 1.2.1: Comparación de alternativas de proyectos.</em></p>


### 1.3 Selección de proyecto

De acuerdo con la ponderación de la Tabla 1.2.1, se eligió implementar el **Controlador Horno Gastronómico Inteligente Completo con STM32**. Esta alternativa aprovecha los periféricos de la placa STM32 (ADC, Timers/PWM, UART, I2C/SPI, GPIOs) e integra la lógica de control en un sistema alimentado por eventos (*Event-Triggered System*).

#### **1.3.1 Diagrama en bloques del sistema**
Para modelar las conexiones de periféricos con el microcontrolador, así cómo también para ejemplificar para el lector los diferentes componentes del prototipo, se diseño el siguiente diagrama en bloques del sistema: 

<img width="3458" height="1696" alt="diagrama_bloques_horno" src="https://github.com/user-attachments/assets/5cb5a1c7-a29f-4e47-aecb-1c4f25c56aa7" />

<p align="center"><em>Figura 1.1: Ilustración del diagrama en bloques del horno eléctrico.</em></p>


## 2. Elicitación de requisitos y casos de uso

### 2.1 Análisis de la competencia y diferenciación del producto

En el mercado argentino existen diversos electrodomésticos y controladores orientados a la cocción y el control térmico. Sin embargo, la gran mayoría se limita a funciones mecánicas tradicionales o carece de una integración completa entre control de potencia, menús locales avanzados e interfaces de comunicación inalámbrica autónomas.

Como primeros competidores directos en el mercado local se identifican:

1. **Hornos Eléctricos Digitales Smart (ej. Yelmo / Smartlife):**
   * Incorporan pantallas LED/LCD simples, selección táctil de resistencias y conectividad Wi-Fi/Bluetooth mediante plataformas globales (como Tuya o Smart Life).
   * **Limitaciones:** Sus aplicaciones móviles son cerradas y genéricas: solo permiten encender/apagar o enviar una temporización simple. No posibilitan la programación de curvas de cocción dinámicas por etapas (por ejemplo: 20 minutos a 180 °C con resistencia inferior, seguidos de 10 minutos a 220 °C con grill y luz dimerizada). Además, su firmware es propietario e inaccesible para mantenimiento o actualización.

2. **Controladores Térmicos y Termostatos Bluetooth/Wi-Fi (ej. Inkbird ITC / Xiaomi Smart Oven):**
   * Dispositivos de control térmico que integran enlaces inalámbricos para monitorear lecturas en tiempo real desde el celular y emitir alertas.
   * **Limitaciones:** Los controladores tipo termostato externo carecen de la interfaz para gestionar los actuadores específicos de un horno (resistencias independientes, sistema de convección, dimerización de luz interna). Por otro lado, los hornos de ecosistemas cerrados requieren conexión obligatoria a servidores en la nube y carecen de almacenamiento físico no volátil en el propio equipo para operar sin conexión a internet.

---

#### Diferenciación y ventajas del prototipo desarrollado

El **Sistema Controlador de Horno Gastronómico Eléctrico Inteligente** diseñado sobre la plataforma **STM32** se destaca por las siguientes ventajas competitivas:

* **Autonomía y Persistencia Local (EEPROM / Flash):** A diferencia de las soluciones comerciales que dependen de servidores en la nube, el sistema almacena las recetas y perfiles de cocción de múltiples etapas directamente en la memoria **EEPROM externa (I2C) o Flash interna**. La curva térmica se ejecuta de forma autónoma sin riesgo de interrupción si se pierde la conexión Bluetooth.
* **Interfaz de Usuario Híbrida y Accesible:** Combina un panel local completo (pantalla LCD 2x16/20x4, teclado matricial, potenciómetro analógico y **Dip Switches** para selección inmediata del modo de calor) con control y monitoreo remoto a través del módulo **HM-10 (BLE)**. El usuario mantiene el control absoluto tanto en modo manual como remoto.
* **Arquitectura de Tiempo Real No Bloqueante (*Bare Metal*):** Su estructura basada en un sistema alimentado por eventos (*Event-Triggered System*), *Super-Loop* y *Tick* de 1 ms garantiza el procesamiento en tiempo real sin bloqueos, permitiendo un control térmico preciso, gestión inmediata de alarmas de seguridad con el **Buzzer** y la adición de periféricos por buses estándar (I2C, SPI, UART).
* **Precio**: Los ejemplos comerciales nombrados, se encuentran entre los de precio más elevado para hornos de esas características debido a la posibilidad de interacción mediante WiFi o Bluetooth. El diseño de controlador presentado por el grupo 02, intenta **reducir costos, manteniendo y ampliando** las funcionalidades que hoy por hoy ofrece el mercado.

Es decir que si bien se presenta un mercado competitivo en términos de alternativas, el diseño propio cuenta con ventajas a explotar y tiene una inserción posible en la industria debido a que no todas las funcionalidades propuestas están siendo ofrecidas por otras marcas o por lo menos no de manera conjunta como lo haría nuestro prototipo.

| Grupo | ID | Descripción |
| :--- | :--- | :--- |
| **Sensores** | 1.1 | El sistema medirá periódicamente la temperatura interna del horno mediante un sensor térmico (NTC / Termocupla) conectado al ADC o SPI del STM32. |
| | 1.2 | El sistema utilizará un potenciómetro analógico (ADC) para el ajuste fino de intensidad de luz interna o de parámetros numéricos. |
| **Interfaz LCD** | 2.1 | El sistema contará con una pantalla LCD (2x16 / 20x4) para visualización del menú navegable. |
| | 2.2 | El sistema mostrará en el LCD una pantalla de bienvenida y estado en reposo (`IDLE`) al encender. |
| | 2.3 | El sistema mostrará en el LCD la temperatura actual medida y la temperatura objetivo durante el proceso de cocción. |
| | 2.4 | El sistema mostrará en el LCD el tiempo restante del temporizador de cocción. |
| | 2.5 | El sistema mostrará un mensaje de alerta explícito en el LCD cuando ocurra una sobretemperatura o falla. |
| **Entradas / Controles**| 3.1 | El sistema contará con un teclado matricial/botones (`Enter`, `Next`, `Escape`) para la navegación del menú y confirmación de valores. |
| | 3.2 | El sistema implementará filtrado de antirrebote (*debounce*) por software para todos los botones de entrada. |
| | 3.3 | El sistema contará con un bloque de **Dip Switches** para la selección directa e instantánea de la fuente de calor (Superior, Inferior, Ambas, Convección). |
| **Indicadores Luminosos**| 4.1 | El sistema contará con un LED indicador para señalar el estado de encendido/reposo (`IDLE`). |
| | 4.2 | El sistema contará con un LED indicador para señalar que las resistencias de calentamiento están activas (`HEATING`). |
| | 4.3 | El sistema contará con un LED indicador para señalar que el horno alcanzó la temperatura objetivo (`SETPOINT OK`). |
| | 4.4 | El sistema notificará condiciones de alarma o falla de seguridad mediante el titileo intermitente de los LEDs indicadores. |
| **Actuadores y Potencia** | 5.1 | El sistema controlará las resistencias de calentamiento (Superior/Inferior) mediante salidas digitales (GPIO) hacia relés/MOSFETs. |
| | 5.2 | El sistema aplicará modulación por ancho de pulso (**PWM**) para la regulación dimérica de la luz interna del horno. |
| **Buzzer** | 6.1 | El sistema emitirá una melodía corta con el Buzzer al confirmar la selección de un menú o inicio de cocción. |
| | 6.2 | El sistema emitirá un patrón sonoro repetitivo con el Buzzer al finalizar el tiempo del temporizador de cocción. |
| | 6.3 | El sistema emitirá un tono continuo de advertencia acústica cuando se detecte sobretemperatura o estado de **FALLA**. |
| **Comunicación BLE** | 7.1 | El sistema se comunicará mediante el módulo **HM-10** (UART) con la aplicación móvil. |
| | 7.2 | El sistema enviará periódicamente a la App la lectura de temperatura interna y el estado del temporizador. |
| | 7.3 | El sistema podrá recibir desde la App comandos de inicio, pausa y parada de emergencia de la cocción. |
| | 7.4 | El sistema podrá recibir desde la App perfiles o parámetros de recetas predefinidas. |
| | 7.5 | El sistema notificará a la App cuando se produzca una alarma de sobretemperatura o cancelación. |
| **Memoria (EEPROM/FLASH)**| 8.1 | El sistema almacenará la configuración de recetas de usuario en memoria **EEPROM externa** (vía bus I2C) o Flash interna. |
| | 8.2 | El sistema recuperará la última configuración válida almacenada en la EEPROM durante la secuencia de inicio de firmware. |
| **Arquitectura Firmware**| 9.1 | El firmware se estructurará de forma modular (*Bare Metal*) bajo el patrón *Super-Loop* (escuchar/procesar/actuar). |
| | 9.2 | El sistema utilizará una base de tiempo estricta (*System Tick* de 1 ms) para la ejecución no bloqueante de tareas temporizadas. |
| | 9.3 | La lógica de control principal se organizará mediante máquinas de estados finitos (*FSM*). |

<p align="center"><em>Tabla 2.1: Requisitos del proyecto.</em></p>

---

### 2.2 Casos de uso

A continuación se presentan 3 casos de usos que creemos de relevancia para dar una idea de funcionamiento del proyecto y sus diferentes alternativas al ser utilizado por el usuario.

#### **Caso de Uso 1: Configuración e inicio de cocción manual desde la interfaz local**

| Elemento | Definición |
| :--- | :--- |
| **Disparador** | El usuario desea iniciar un proceso de cocción configurando parámetros desde el panel frontal. |
| **Precondiciones** | El sistema está encendido en estado **IDLE/NORMAL**. El horno está a temperatura ambiente y la puerta cerrada. |
| **Flujo Principal** | 1. El usuario configura los **Dip Switches** para elegir la distribución de calor (ej: Resistencia Superior + Inferior).<br>2. El usuario navega en el **LCD** con el teclado para fijar la temperatura objetivo y el tiempo de cocción.<br>3. El usuario utiliza el potenciómetro para ajustar la intensidad de iluminación interna.<br>4. El usuario confirma el inicio con el botón "Enter".<br>5. El sistema activa el control térmico, enciende los LEDs de calentamiento e inicia el temporizador.<br>6. Al finalizar el tiempo, el sistema apaga los elementos calefactores y hace sonar el **Buzzer**. |
| **Flujos Alternativos** | **a. Alarma de Sobretemperatura:** Si la temperatura medida excede el límite máximo de seguridad, el sistema interrumpe el calentamiento, pasa a estado de **FALLA**, emite un tono continuo con el Buzzer y muestra la alerta en el LCD.<br>**b. Cancelación manual:** Si el usuario presiona el botón "Escape", el proceso se detiene y regresa al menú principal. |
<p align="center"><em>Tabla 2.2.1: Caso de Uso 1 del proyecto.</em></p>

#### **Caso de Uso 2: Control y monitoreo remoto vía Bluetooth (App)**

| Elemento | Definición |
| :--- | :--- |
| **Disparador** | El usuario envía un comando o consulta el estado del horno desde la aplicación móvil. |
| **Precondiciones** | El módulo Bluetooth **HM-10** está emparejado correctamente con el dispositivo móvil. El sistema está encendido. |
| **Flujo Principal** | 1. La aplicación solicita la lectura actual de temperatura e información de estado.<br>2. El microcontrolador responde vía UART con los datos estructurados.<br>3. El usuario selecciona una "Receta Predefinida" en la App y presiona "Iniciar Receta".<br>4. La App transmite la temperatura objetivo y el tiempo a través de comandos de texto por Bluetooth.<br>5. El sistema confirma la recepción mediante un tono breve en el **Buzzer**, actualiza la pantalla LCD y comienza el proceso. |
| **Flujos Alternativos** | **a. Pérdida de Conexión BLE:** Si se desconecta la App durante una cocción en curso, el horno continúa ejecutando el perfil previamente cargado en memoria de forma segura. |
<p align="center"><em>Tabla 2.2.2: Caso de Uso 2 del proyecto.</em></p>

#### **Caso de Uso 3: Interrupción remota de emergencia del proceso de cocción**

| Elemento | Definición |
| :--- | :--- |
| **Disparador** | El usuario detecta una anomalía de forma presencial (por ejemplo, olor a quemado o humo) o remota y decide cancelar inmediatamente la cocción desde la aplicación móvil. |
| **Precondiciones** | El sistema está encendido y ejecutando un ciclo activo de cocción (calentamiento en curso). El módulo Bluetooth **HM-10** mantiene la conexión activa con la App del usuario. |
| **Flujo Principal** | 1. El usuario presiona el botón de "Parada de Emergencia / Cancelar Cocción" en la aplicación móvil.<br>2. La App transmite un comando de interrupción crítica vía Bluetooth al microcontrolador.<br>3. El STM32 procesa la orden inmediatamente mediante la arquitectura por eventos (*Event-Triggered System*).<br>4. El microcontrolador desactiva las salidas de control de las resistencias (Relés/MOSFETs) y de la luz interna.<br>5. El sistema emite una secuencia sonora de advertencia breve mediante el **Buzzer** y actualiza el LCD mostrando el mensaje `"COCCIÓN CANCELADA"`.<br>6. El sistema regresa al estado de reposo (**IDLE/NORMAL**) de forma segura. |
| **Flujos Alternativos** | **a. Pérdida de conexión en el momento del envío:** Si el comando no se transmite por falla de la señal Bluetooth, el usuario puede presionar el botón "Escape" o la tecla de parada en el panel local del horno para interrumpir la cocción de forma manual e inmediata.<br>**b. Cancelación automática por sobretemperatura:** Si la temperatura medida por el sensor excede el límite máximo de seguridad antes o durante la orden manual, el sistema aborta el ciclo de forma autónoma pasando a estado de **FALLA**. |
<p align="center"><em>Tabla 2.2.3: Caso de Uso 3 del proyecto.</em></p>


