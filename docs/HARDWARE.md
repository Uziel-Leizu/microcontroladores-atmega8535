# Documentación de hardware

Todas las prácticas se hicieron sobre una misma plataforma: el **ATmega8535** de
Atmel (hoy Microchip). Este documento resume las características relevantes,
el pinout del encapsulado de 40 pines y cómo se usó cada puerto en cada
proyecto.

## 1. El microcontrolador ATmega8535

| Parámetro | Valor |
|---|---|
| Arquitectura | AVR (Harvard modificada), 8 bits |
| Reloj | 0 – 16 MHz (en estas prácticas: **1 MHz** o **8 MHz**) |
| Flash | **8 KB** de programa |
| SRAM | **512 bytes** (128 bytes de pila de datos en esta configuración) |
| EEPROM | **4 KB** (512 × 8 bits), se borra y escribe byte a byte |
| Puertos | 4 × 8 bits = **32 líneas E/S** (`PORTA`–`PORTD`) |
| ADC | 8 canales de 10 bits, referencia a `AVcc` o a `AREF` externa |
| USART | 1 puerto síncrono/asíncrono |
| SPI | 1 maestro/esclavo |
| TWI (I²C) | 1 interfaz |
| Timers | T/C0 (8 bits), T/C1 (16 bits), T/C2 (8 bits) |
| Interrupciones | 21 vectores (`INT0`–`INT2`, timers, ADC, USART, TWI, SPI) |

### Organización de memoria (sin Boot Loader)

```
0x0000 - 0x001F  Vectores de interrupción
0x0020 - 0x0FEF  Memoria de programa (Flash)
                 CodeVisionAVR Evaluation limita a 8192 bytes
0x0060 - 0x00DF  Pila de datos (128 bytes en esta configuración)
0x00E0 - 0x025F  Pila de hardware (384 bytes)
```

> Las prácticas de este repositorio **no usan interrupciones**: todo el
> temporizado se hace por software con `delay_ms()` / `delay_us()` dentro de un
> bucle `while(1)`. Los vectores de interrupción quedan deshabilitados porque
> CodeWizard genera esa inicialización por defecto.

## 2. Pinout del ATmega8535 (DIP-40)

| Pin | Nombre | Puerto / función alternativa |
|----:|--------|-------------------------------|
| 1–8 | `PB0`–`PB7` | **Port B** — SPI (`MOSI`/`MISO`/`SCK`), `SS`, timers |
| 9 | `RESET` | Reinicio por pulso bajo (mínimo 1.5 µs) |
| 10 | `VCC` | **+5 V** |
| 11 | `GND` | Tierra |
| 12 | `XTAL2` | Salida de cristal / reloj externo |
| 13 | `XTAL1` | Entrada de cristal / reloj externo |
| 14–21 | `PD0`–`PD7` | **Port D** — `INT0`/`INT1`/`INT2`, `RXD`/`TXD`, comparador analógico |
| 22–29 | `PC0`–`PC7` | **Port C** — `TOSC0`/`TOSC1`, `SCL`/`SDA` (TWI) |
| 30 | `AVCC` | Alimentación del Puerto A y del ADC |
| 31 | `GND` | Tierra |
| 32 | `AREF` | Referencia analógica del ADC |
| 33–40 | `PA0`–`PA7` | **Port A** — entradas del ADC (`ADC0`–`ADC7`) |

**Correlación puerto ↔ patilla física**

| Puerto | Patillas DIP-40 |
|---|---|
| `PORTB` | 1–8 |
| `PORTD` | 14–21 |
| `PORTC` | 22–29 |
| `PORTA` | 33–40 |

Esto es especialmente útil en las prácticas donde los pines del microcontrolador
no coinciden con la nomenclatura del integrado externo: por ejemplo, en el
**teclado matricial** (práctica 10) la figura del enunciado suele numerar las
teclas, no los pines.

> **Alcance de los puertos.** `PORTA` sirve como entrada analógica del ADC y
> también como E/S bidireccional de 8 bits cuando el ADC no se usa. `PORTA`,
> `PORTB`, `PORTC` y `PORTD` cuentan con resistencias de pull-up internas
> activables bit a bit, y sus búferes de salida son simétricos tanto en
> source como en sumidero.

## 3. Configuración típica de un puerto

Todo programa generado por CodeWizard empieza con una sección de inicialización
así. Las dos líneas que importan son:

```c
DDRx  = 0x00;   // 0 = entrada, 1 = salida  ->  define la dirección del pin
PORTx = 0xFF;   // valor en reposo          ->  activa los pull-ups internos
```

En estos proyectos un mismo puerto cambia de función entre prácticas, y es
normal: **`DDR` se reconfigura por completo en cada proyecto**, nunca hay que
asumir nada de una práctica a otra.

### Los dos tipos de display

Los displays de 7 segmentos se conectaron de **dos maneras distintas**, y esa
diferencia es la que explica la doble asignación que aparece en varias
prácticas (`PORTC = tabla[n];` seguida de `PORTA = ~tabla[n];`):

| Tipo | Puerto | Código | Nivel que enciende |
|---|---|---|---|
| **Cátodo común** | `PORTC` (directo) | `PORTC = tabla[n];` | 1 |
| **Ánodo común** | `PORTA` (siempre invertido) | `PORTA = ~tabla[n];` | 0 |

Estuvo presente en las prácticas 03, 04, 05, 06, 07 y 08: un display ánodo común
en el puerto A y uno cátodo común en el puerto C, para comprobar ambos casos.

## 4. Mapa de uso de puertos por proyecto

Extraído directamente de las líneas `DDRx = ...` de cada fuente. Leyenda:
`S` = salida, `E` = entrada, `E*` = entrada con pull-up, `pu` = pulsador,
`disp` = display de 7 segmentos, `IR` = infrarrojo, `adc` = entrada analógica.

| Carpeta | `PORTA` | `PORTB` | `PORTC` | `PORTD` |
|---|---|---|---|---|
| `practica-01-puertos-e-s` | — | **E*** | — | **S** |
| `practica-02-contador-asc-desc` | **S** | — | **S** | **S** |
| `practica-03-bcd-a-7-segmentos` | **S** disp ánodo | **E*** nibble BCD + selector | **S** disp cátodo | **S** |
| `practica-04-contador-0-9` | **S** disp ánodo | **E*** pu `PB0` | **S** disp cátodo | **S** |
| `practica-05-contador-por-flancos` | **S** disp ánodo | **E*** pu `PB0` | **S** disp cátodo | **S** |
| `practica-06-contador-sin-rebotes` | **S** disp ánodo | **E*** pu `PB0` | **S** disp cátodo | **S** |
| `practica-07-contador-00-99` | **S** disp ánodo | — | **S** disp cátodo | **S** salvo `PD0` = **E*** |
| `practica-08-cronometro-60s` | **S** díg. decenas | **E*** control (`&0x07`) | **S** díg. unidades | **S** díg. décimas |
| `proyecto-01-emisor-v1` | **S** motor | — | — | **E*** 4 pu (`PD0`–`PD3`) |
| `proyecto-01-emisor-v2` | **S** motor | `PB4` = **E**, resto **S** | — | **E*** 4 pu (`PD0`–`PD3`) |
| `proyecto-01-receptor` | **E*** IR en `PA0` | — | **S** LEDs | **S** |
| `practica-10-teclado-matricial` | — | **S** | **S** `PC0`–`PC3` / **E*** `PC4`–`PC7` | **S** disp |
| `practica-11-memoria-eeprom` | **E*** pu `PA0` | **S** | **S** `PC0`–`PC3` / **E*** `PC4`–`PC7` | **S** disp |
| `proyecto-02-bee-bot` | **E*** 7 pu | **S** | **S** LEDs | **S** |
| `practica-13-convertidor-adc` | **E*** adc `PA0` | — | — | **S** 8 bits |
| `practica-14-vumetro` | **E*** adc `PA0`, `PA1` | — | **S** canal A | **S** canal B |
| `practica-16-termometro-digital` | **E*** adc `PA0` | **S** díg. 1 | **S** selectores | **S** díg. 2 y 3 |

> `proyecto-01-emisor-v1/prueba.c` es un esqueleto vacío: todos los puertos
> quedan como salida por la inicialización automática y el bucle `while(1)` no
> hace nada.

### Configuración del ADC en las tres prácticas analógicas

| Práctica | `ADEN` | Prescalizador | Referencia | `ADLAR` |
|---|---|---|---|---|
| 13 — Convertidor ADC | 1 | ÷64 | **AREF externa** | 1 (izquierda) |
| 14 — Vúmetro | 1 | ÷128 | **AVcc** | 1 (izquierda) |
| 16 — Termómetro | 1 | ÷128 | **AVcc** en `P16.c`/`P164.c`; **AREF** en `P162.c`/`P163.c` | 1 (izquierda) |

Con `ADLAR = 1` el resultado se justifica a la izquierda, por lo que `ADCH`
contiene los **8 bits más significativos** de la conversión de 10 bits. De ahí
que `read_adc()` devuelva un rango de 0 a 255.

### El multiplexado del termómetro (práctica 16)

Es el mapeo más complejo del repositorio. Se muestra un solo dígito a la vez,
seleccionado por las líneas de `PORTC`:

```
Iteración   PORTC   Dato mostrado
     1     0b001    primer dígito   -> PORTB
     2     0b010    segundo dígito  -> PORTD
     3     0b100    tercer dígito   -> PORTD
```

Las cuatro variantes (`P16.c`, `P162.c`, `P163.c`, `P164.c`) invierten el orden
del selector y cambian los retardos entre iteraciones. Ver
[NOTAS-TECNICAS.md](NOTAS-TECNICAS.md).

## 5. Materiales usados

### Comunes a todas las prácticas

| Cant. | Material |
|---|---|
| 1 | Microcontrolador **ATmega8535** en encapsulado DIP-40 |
| 1 | Placa de pruebas o protoboard |
| 1 | Programador **AVRISP mkII** o **USBasp** |
| 1 | Cristal de 8 MHz + 2 condensadores de 22 pF (para las prácticas de 8 MHz) |
| 1 | Fuente regulada de 5 V |
| — | Resistencias de 330 Ω a ¼ W para limitación de corriente de los displays |

### Por práctica

| Práctica | Material adicional |
|---|---|
| 01 | 8 LEDs + 8 resistencias de 330 Ω |
| 02 | 16 LEDs + 16 resistencias |
| 03 | 2 displays de 7 segmentos (1 ánodo común, 1 cátodo común) + 1 interruptor |
| 04 | 1 display + 1 pulsador |
| 05 | 1 display + 1 pulsador |
| 06 | 1 display + 1 pulsador |
| 07 | 2 displays cátodo común, 1 par infrarrojo (emisor y receptor), transistor **2N2222**, 1 resistencia de 680 Ω, 1 de 1 kΩ |
| 08 | 3 displays cátodo común + 3 pulsadores |
| 09 (P9) | 2 microcontroladores, emisor y receptor infrarrojo, 4 pulsadores, motor con driver |
| 10 | Teclado matricial 4×4 (16 pulsadores) + 1 display |
| 11 | Lo mismo que 10 + 1 pulsador adicional para guardar en EEPROM |
| 12 | Placa de robot *Bee-Bot* (o 4 motores), 7 pulsadores, LEDs indicadores |
| 13 | 1 potenciómetro como fuente de tensión variable para el ADC + 8 LEDs |
| 14 | 2 potenciómetros (o 2 señales de audio), 2 barras de LEDs, 3 pulsadores |
| 16 | 1 sensor de temperatura (o potenciómetro), 3 displays de 7 segmentos, 1 pulsador |
| — | Práctica de analógico: puente de Wheatstone (4 resistencias + potenciómetro de precisión), amplificador operacional **LM741** con ±7 V, 4 resistencias de ajuste de ganancia. **Sin firmware asociado** |

## 6. Tabla de conversión de 7 segmentos

Es la tabla `tabla7segmentos` / `tablaHex` que aparece en las prácticas 03, 07,
08, 13 y 16. Los bits van de MSB a LSB con el orden `g f e d c b a`:

| Índice | Cátodo común | Ánodo común (`~valor`) | Carácter |
|--------:|---------------|--------------------------|----------|
| `0` | `0x3F` | `0xC0` | `0` |
| `1` | `0x06` | `0xF9` | `1` |
| `2` | `0x5B` | `0xA4` | `2` |
| `3` | `0x4F` | `0xB0` | `3` |
| `4` | `0x66` | `0x99` | `4` |
| `5` | `0x6D` | `0x92` | `5` |
| `6` | `0x7D` | `0x82` | `6` |
| `7` | `0x07` | `0xF8` | `7` |
| `8` | `0x7F` | `0x80` | `8` |
| `9` | `0x6F` | `0x90` | `9` |
| `10` | `0x77` | `0x88` | `A` |
| `11` | `0x7C` | `0x83` | `b` |
| `12` | `0x39` | `0xC6` | `C` |
| `13` | `0x5E` | `0xA1` | `d` |
| `14` | `0x79` | `0x86` | `E` |
| `15` | `0x71` | `0x8E` | `F` |

Los índices 10–15 solo existen en `tablaHex`. Son los que hacen falta en la
práctica 03 (selección hexadecimal) y en el termómetro, donde los caracteres de
unidad no son dígitos.

## Referencias

- [Datasheet ATmega8535][ds] — Atmel / Microchip (figura *Pinout ATmega8535*)
- [CodeVisionAVR][cv] — HP InfoTech
- [Tutorial de CodeVisionAVR en español][tut] — HyperLynx
- [Manual de la placa OLIMEX AVR-P40-8535][olimex] — confirma el uso de los
  pines 12 (`XTAL2`) y 13 (`XTAL1`) para el cristal de 8 MHz

[ds]: https://ww1.microchip.com/downloads/en/DeviceDoc/2502S.pdf
[cv]: https://www.hpinfotech.com/cvavr.html
[tut]: https://www.hyperlynx.com.br/tutorial-avr-codevision-avr
[olimex]: https://www.olimex.com/Products/AVR/Proto/AVR-P40-8535-8MHz/resources/AVR-P40-8535.pdf

---

Volver al [índice](../README.md) · Ver [PROYECTOS.md](PROYECTOS.md) ·
[COMPILACION.md](COMPILACION.md) · [NOTAS-TECNICAS.md](NOTAS-TECNICAS.md) ·
[GLOSARIO.md](GLOSARIO.md)