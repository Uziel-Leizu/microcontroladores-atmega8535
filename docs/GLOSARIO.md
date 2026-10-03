# Glosario

Términos técnicos usados en este repositorio, ordenados alfabéticamente. Las
definiciones son divulgativas: describen qué es cada cosa y por qué aparece en
estas prácticas, no la especificación completa del microcontrolador.

---

## A

**ADC** (*Analog-to-Digital Converter*, convertidor analógico a digital).
Componente que toma una tensión y devuelve un número. El ATmega8535 tiene un ADC
de **10 bits**, así que su rango es 0 a 1023. Se usa en las prácticas 13, 14 y
16 para leer sensores.

**Ancho de banda** — rango de frecuencias que un circuito deja pasar. En la
práctica 16 el multiplexado del display debe ser suficientemente rápido para que el ojo
no note parpadeos.

**Anti-rebote** (*debouncing*) — técnica para que una pulsación humana cuente una
sola vez. Un pulsador mecánico genera entre 5 y 20 ms de oscilaciones al
presionarse; el microcontrolador, que corre a MHz, las ve todas. Se resuelve con
espera activa (prácticas 05, 06 y 12) o comparando con el estado anterior del
pin (práctica 14).

**Arduino** — plataforma con microcontrolador ATmega328P y lenguaje C++ muy
simplificado. **No se usa aquí**: el ATmega8535 es un AVR de 8 bits clásico, sin
bootloader ni entorno de Arduino.

**ATmega8535** — microcontrolador AVR de 8 bits de Atmel (hoy Microchip).
Dip-40, 8 KB de Flash, 512 bytes de SRAM y 4 KB de EEPROM. Es el chip de todas
estas prácticas.

**AVR** — familia de microcontroladores RISC de Atmel. Los AVR de 8 bits tienen
un juego de instrucciones reducido y un mapa de registros compacto, lo que los
hace muy populares en Lazarus, Atmel Studio y CodeVisionAVR.

**AVRDUDE** — herramienta de línea de comandos para leer, escribir y verificar
la Flash y la EEPROM de un AVR mediante un programador (USBasp, AVRISP mkII…).

---

## B

**Baud** — velocidad de transmisión en bits por segundo de un puerto serie. En
el Proyecto 1 se usa 9600 baud para enviar caracteres ASCII por USART.

**BCD** (*Binary Coded Decimal*, decimal codificado en binario) — forma de
representar un dígito decimal (0–9) en 4 bits. El 5 se guarda como `0101`, no
como binario natural. Es lo que permite a un display de 7 segmentos mostrar un
número decimal.

**`.cfg`** — archivo de configuración que genera CodeWizardAVR al crear un
proyecto. **No se versiona**: es un artefacto derivado.

---

## C

**C (lenguaje)** — el lenguaje de los 23 firmware. En realidad es un dialecto
de C estándar, llamado **AVR C**, que añade el calificador `eeprom` y las
palabras clave `bit`, `sfr` y `sbit` para acceder a los registros del
microcontrolador.

**Calidad de código** — conjunto de criterios con los que se evaluaban las
prácticas: que el enunciado y el código coincidan, que no haya rebotes, que no
se lean variables sin inicializar, y que el reloj del software coincida con el
del hardware.

**CodeVisionAVR** — IDE comercial de Atmel para AVR, con el generador
CodeWizardAVR integrado. Es con el que se hicieron estas prácticas. La versión
*3.32 Evaluation* compila hasta 8 KB.

**CodeWizardAVR** — asistente que genera el esqueleto de un proyecto:
`main()`, configuración de puertos, inicialización del ADC, del USART y del
Watchdog, y los `#define` con los pines. Ese esqueleto aparece al principio de
todos los `.c` de este repositorio.

**Compilador** — programa que traduce el código fuente (`.c`) a código máquina
(`.hex` / `.elf`) ejecutable. Aquí: CodeVisionAVR 3.32 o `avr-gcc`.

**Cristal** — componente de cristal de quarts que da la referencia de reloj al
microcontrolador. Se conecta entre los pines 12 (XTAL2) y 13 (XTAL1). Una
frecuencia incorrecta hace que **todos** los retardos por software sean
proporcionalmente incorrectos.

---

## D

**DIP-40** — encapsulado con 40 pines en dos filas paralelas. Todos los diagramas
de este repositorio usan la numeración de un DIP-40, con pines 1–10 de un lado
en sentido antihorario y 11–40 del otro.

**Display de 7 segmentos** — display con 7 barras (a–g) más punto decimal (`dp`).
Con los 7 segmentos se representan los 10 dígitos y las letras A–F en
hexadecimal.

**Duty cycle** (*ciclo de trabajo*) — proporción del tiempo en que una señal
permanece activa. En multiplexado de displays, un dígito de `n` tiene un duty
cycle de `1/n`.

---

## E

**EEPROM** — *Electrically Erasable Programmable Read-Only Memory*. Memoria
**no volátil** (conserva el contenido sin alimentación) y escribible byte a
byte. El ATmega8535 tiene 4 KB. La práctica 11 la usa; la práctica 12, pese a
tomar su enunciado de la 11, no.

**`.eep`** — archivo binario con el contenido inicial de la EEPROM, generado al
compilar. Se graba con `avrdude -U eeprom:w:...` **además** del `.hex`.

---

## F

**Frecuencia de reloj** — velocidad a la que el microcontrolador ejecuta
instrucciones. Define la duración real de cualquier retardo por software. Aquí
hay proyectos a 1 MHz y a 8 MHz, y esa diferencia es la causa de que algunos no
funcionen al grabarlos tal cual. Ver
[COMPILACION.md § 5](COMPILACION.md#frecuencias-de-reloj-por-proyecto).

**Fuse** — byte de configuración del microcontrolador que se programa **una sola
vez** con un programador externo, no desde el programa. Los relevantes aquí son
`CKSEL` y `CKOPT`, que seleccionan la fuente del reloj externo.

**Flash** — memoria de programa. En el ATmega8535 son 8 KB y no se puede
escribir desde el propio programa. Altera el contenido desde un programador.

---

## H

**Hz** — hercio, ciclos de reloj por segundo. 1 MHz = 1 000 000 ciclos/s.

---

## I

**Interrupción** — mecanismo que detiene el flujo normal del programa para
atender un evento (un flanco de pin, el fin de una conversión del ADC, el
desbordamiento de un timer). Permite responder sin consultar el pin en un bucle.

---

## L

**LCD** — *Liquid Crystal Display*, display de cristal líquido con controlador
interno. **No se usó en estas prácticas**: todos los displays son de 7
segmentos.

**LED** — *Light Emitting Diode*, diodo que emite luz. En las prácticas 14 y 16
se usan en barra o como display de 7 segmentos (pines 10–16 del encapsulado).

---

## M

**Multiplexado** — técnica para mostrar varios dígitos con un mismo juego de
líneas. Se enciende **un solo dígito a la vez**, cambiando de selector tan
rápido que el ojo percibe todos encendidos a la vez. La práctica 16 recorre los
selectores del termómetro digital con un retardo entre cada uno para que las
transiciones sean visibles.

---

## P

**Periferico / Puerto** — agrupamiento de 8 pines del microcontrolador que se
pueden configurar como entradas o salidas independientemente. El ATmega8535
tiene cuatro: `PA` (pines 33–40), `PB` (1–8), `PC` (22–29) y `PD` (14–21).

**PID** — *Proportional-Integral-Derivative*. Regla de control usada para
mantener constante una magnitud. En el Proyecto 2 el enunciado la menciona, pero
**el código entregado no la implementa**.

**PWM** — *Pulse Width Modulation*. Modulación del ancho de pulso para simular un
nivel analógico con salidas digitales. En el Proyecto 1 se usa a 38 kHz, por
encima del oído humano, para que el motor no emita ruido.

**`.prj`** — archivo de proyecto de CodeVisionAVR: chip, reloj, fuses y opciones
del compilador. Es el que se abre desde *Project → Open Project*.

**`.atsln`** — solución de Atmel Studio. Contiene la referencia al `.cproj`
con ruta relativa.

**`.cproj`** — proyecto de código de Atmel Studio. Declara qué `.c` se compilan.

**Pull-up** — resistencia que lleva un pin a Vcc cuando nadie lo fuerza a GND.
Permite que un pulsador enviado a GND se lea como «soltado = 1, pulsado = 0»,
que es la convención que usa la práctica 14.

---

## R

**RC interno** — oscilador interno calibrado de fábrica. No requiere cristal
externo. Es el reloj por defecto del ATmega8535.

**`REG` / `REG1` / `REG2`** — los tres registros de 8 bits del ATmega8535
(`R0`–`R7` en ensamblador), usados como variables de 8 bits.

**Retardo por software** — bucle vacío que consume tiempo. Su precisión es
inversamente proporcional a la frecuencia de reloj: a 1 MHz, un bucle de N
iteraciones dura N microsegundos aproximadamente.

---

## S

**SPI** — interfaz serie síncrona de 4 hilos. La usa el **Proyecto 2** para
hablar con el sensor VL53L0X del Bee-Bot.

**SRAM** — *Static RAM*. Memoria volátil de datos: 512 bytes en el
ATmega8535. Al declarar `char buffer[10]` se consumen 10 bytes de ella, y una
declaración de más de 512 bytes rompe el programa sin avisar.

---

## T

**Timer** — contador de hardware que mide intervalos sin ocupar la CPU. Útil
para retardos precisos y para medir duraciones. Las prácticas finales lo usan
en lugar de estimar el tiempo con retardos por software.

**Tolerancia** — variación permitida de un valor. Los cristales de 8 MHz
llevan una tolerancia de ±50 ppm.

---

## U

**USART** — *Universal Synchronous/Asynchronous Receiver-Transmitter*. Puerto
serie asíncrono. En el ATmega8535 tiene los pines **10 (RXD)** y **11 (TXD)**
del puerto `PD`. Se usa en el Proyecto 1 para enviar los pulsos infrarrojos al
receptor, y en la práctica 16 para otra salida de depuración.

---

## V

**Variable entera sin signo (`unsigned char`)** — número de 0 a 255. Ocupa un
byte. Es el tipo más usado en estas prácticas porque los contadores van de 0 a
99 y los dígitos de 7 segmentos de 0 a 15.

**Vref** — tensión de referencia del ADC. En el ATmega8535 puede ser AVcc, la
referencia interna de 2.56 V o un pin externo (pin 32, AREF). Elegir mal la
referencia cambia la escala de todas las lecturas, aunque el código se vea
correcto.

---

## W

**Watchdog** — *perro guardián*. Temporizador interno que reinicia el
microcontrolador si el programa se cuelga. CodeWizardAVR lo puede activar al
generar un proyecto; en estas prácticas se dejó desactivado, probablemente para
poder depurar sin reinicios inesperados.

---

## Términos del lenguaje AVR C

Estas palabras clave **no existen en C estándar**. Son la razón por la que el
`avr-gcc` de línea de comandos no compila los `.c` de este repositorio tal cual,
y la razón por la que la práctica 11 requiere CodeVisionAVR en lugar de Atmel
Studio.

| Término | Significado |
|---|---|
| **`bit`** | Tipo de dato de 1 bit. Una variable `bit` vale 0 o 1. |
| **`eeprom`** | Calificador que ubica una variable en la EEPROM en lugar de la SRAM. `eeprom unsigned char contador;` |
| **`sbit`** | Tipo de dato para un bit suelto de un registro de 8 bits. `sbit btn = PINB.0;` |
| **`sfr`** | *Special Function Register*. Acceso a un registro de I/O. `sfr DDRB = 0x04;` |
| **`#define`** | Dirección de un puerto, alias de una constante, o dirección de memoria. `#define btn PINB.0` |
| **`volatile`** | Indica al compilador que la variable puede cambiar sin que lo sepa. Necesario para leer registros dentro de un bucle. |

---

## Ver también

- [PROYECTOS.md](PROYECTOS.md) — ficha de cada práctica
- [HARDWARE.md](HARDWARE.md) — pinout, puertos y materiales
- [COMPILACION.md](COMPILACION.md) — cómo compilar y grabar
- [NOTAS-TECNICAS.md](NOTAS-TECNICAS.md) — inconsistencias encontradas
- [README.md](../README.md) — índice y guía del repositorio
