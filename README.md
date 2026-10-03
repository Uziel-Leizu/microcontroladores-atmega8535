<div align="center">

# Prácticas de Microcontroladores — ATmega8535

**Colección de 23 programas en C para el microcontrolador ATmega8535**

*Materia «Sistemas en Chip» · IPN — Escuela Superior de Computación*

[![Licencia MIT](https://img.shields.io/badge/Licencia-MIT-2ea44f?style=flat-square&logo=opensourceinitiative&logoColor=white)](LICENSE)
![Lenguaje](https://img.shields.io/badge/Lenguaje-C%20(AVR--GCC%20%2F%20CodeVision)-blue?style=flat-square)
![Microcontrolador](https://img.shields.io/badge/Microcontrolador-ATmega8535-orange?style=flat-square)
![IDE](https://img.shields.io/badge/IDE-CodeVisionAVR%203.32-lightgrey?style=flat-square)
![Estado](https://img.shields.io/badge/Estado-Completo-brightgreen?style=flat-square)

</div>

---

## Descripción

Repositorio con **23 programas en C** escritos para el **ATmega8535** durante la
materia *Sistemas en Chip* (Microcontroladores) del **Instituto Politécnico
Nacional, Escuela Superior de Computación**.

El material cubre, en orden de complejidad:

- configuración de **puertos de entrada/salida** y manipulación de bits;
- displays de **7 segmentos** (ánodo y cátodo común) y tablas de conversión;
- **detección de flancos** y **eliminación de rebotes** en pulsadores;
- **teclado matricial** 4×4 multiplexado;
- **memoria EEPROM** interna del microcontrolador;
- **convertidor analógico-digital (ADC)** de 10 bits;
- multiplexado de displays y aplicación de temporizadores por software;
- dos **proyectos integradores**: un móvil controlado por infrarrojo con dos
  microcontroladores comunicados, y un robot grabador/reproductor de
  trayectorias tipo *Bee-Bot*.

Cada práctica tiene su carpeta con el código fuente, la solución para
CodeVisionAVR / Atmel Studio, y — cuando existe — su reporte correspondiente en
[`reportes/`](reportes/).

## Autor

**Francisco Uziel Córdova Pichardo**

| | |
|---|---|
| Institución | Instituto Politécnico Nacional — Escuela Superior de Computación |
| Materia | Sistemas en Chip (Microcontroladores) |
| Periodo | 2022 – 2023 |
| Contacto | GitHub — [@Uziel-Leizu](https://github.com/Uziel-Leizu) |

## Índice

- [Herramienta y hardware](#herramienta-y-hardware)
- [Resumen de proyectos](#resumen-de-proyectos)
- [Mapa de proyectos por tema](#mapa-de-proyectos-por-tema)
- [Cómo compilar y grabar](#cómo-compilar-y-grabar)
- [Estructura del repositorio](#estructura-del-repositorio)
- [Documentación](#documentación)
- [Reportes](#reportes)
- [Limitaciones conocidas](#limitaciones-conocidas)
- [Licencia](#licencia)

---

## Herramienta y hardware

| Concepto | Detalle |
|---|---|
| **Microcontrolador** | Atmel/Microchip **ATmega8535** (AVR, 8 bits) |
| **Memoria Flash** | 8 KB (8192 bytes) |
| **SRAM** | 512 bytes |
| **EEPROM** | 4 KB (512 × 8 bits) |
| **Reloj** | 1 MHz en la mayoría de prácticas; **8 MHz** en `practica-13`, `practica-14` y `practica-16` |
| **IDE principal** | **CodeVisionAVR 3.32 Evaluation** (HP InfoTech) |
| **IDE alternativo** | **Atmel Studio 7** — las soluciones `.atsln` son compatibles |
| **Lenguaje** | C (extensión AVR-GCC) |
| **Programador** | AVRISP mkII / USBasp con **AVRDUDE** |
| **Flash máx. usada** | 700 bytes (`proyecto-02-bee-bot`) = **8.5 %** del microcontrolador |

> [!IMPORTANT]
> **La frecuencia de reloj es inconsistente entre prácticas.** Los retardos
> `delay_ms()` / `delay_us()` dependen por completo de ella. Un `.hex`
> compilado para 1 MHz **no funciona** en una placa con cristal de 8 MHz (ni
> viceversa). Ver [`docs/NOTAS-TECNICAS.md`](docs/NOTAS-TECNICAS.md).

> [!NOTE]
> Los artefactos de compilación (`.hex`, `.elf`, `.eep`, `.o`, `.map`, `Debug/`,
> `Release/`) están **excluidos por `.gitignore`**. Recompila para generarlos.

## Resumen de proyectos

23 archivos fuente distribuidos en 17 carpetas. La columna *Flash* indica el
tamaño de programa reportado por CodeVisionAVR.

| # | Carpeta | Práctica | Tema | Periféricos clave | Flash | Reporte |
|---|---------|----------|------|-------------------|-------|-----------|
| 1 | [`practica-01-puertos-e-s`](practica-01-puertos-e-s/) | Práctica 01 | Puertos de E/S | `PORTB` → `PORTD` | 184 B | [PDF](reportes/Practica01%20C%C3%B3rdova.pdf) |
| 2 | [`practica-02-contador-asc-desc`](practica-02-contador-asc-desc/) | Práctica 02 | Contador ascendente y descendente | `PORTC++`, `PORTD--` | 220 B | [PDF](reportes/Practica02%20Cordova.pdf) |
| 3 | [`practica-03-bcd-a-7-segmentos`](practica-03-bcd-a-7-segmentos/) | Práctica 03 | Convertidor BCD → 7 segmentos | `PORTB`, `PORTC`, `PORTA` | 314 B | [PDF](reportes/Practica03%20Cordova.pdf) |
| 4 | [`practica-04-contador-0-9`](practica-04-contador-0-9/) | Práctica 04 | Contador 0–9 con pulsador | pulsador + 7 segmentos | 228 B | [PDF](reportes/Practica04%20Cordova.pdf) |
| 5 | [`practica-05-contador-por-flancos`](practica-05-contador-por-flancos/) | Práctica 05 | Contador activado por flancos | detección de flanco 1→0 | 298 B | [PDF](reportes/Practica05_6%20Cordova.pdf) |
| 6 | [`practica-06-contador-sin-rebotes`](practica-06-contador-sin-rebotes/) | Práctica 06 | Contador **sin rebotes** | `delay_ms(40)` antirebote | 340 B | [PDF](reportes/Practica05_6%20Cordova.pdf) |
| 7 | [`practica-07-contador-00-99`](practica-07-contador-00-99/) | Práctica 07 | Contador 00–99 (par infrarrojo) | 2 dígitos, `PIND.0` | 350 B | [PDF](reportes/Practica07%20Cordova.pdf) |
| 8 | [`practica-08-cronometro-60s`](practica-08-cronometro-60s/) | Práctica 08 | Cronómetro de 60 s | 3 dígitos + 3 pulsadores | 372 B | [PDF](reportes/Practica08%20Cordova.pdf) |
| 9 | [`proyecto-01-emisor-v1`](proyecto-01-emisor-v1/) | Proyecto 1 | Emisor infrarrojo (v1) | 4 pulsadores, control de motor | 286 B | [PDF](reportes/Proyecto%201.pdf) |
| 10 | [`proyecto-01-emisor-v2`](proyecto-01-emisor-v2/) | Proyecto 1 | Emisor infrarrojo (v2) | 4 pulsadores, pulsos de 500–1100 µs | 290 B | [PDF](reportes/Proyecto%201.pdf) |
| 11 | [`proyecto-01-receptor`](proyecto-01-receptor/) | Proyecto 1 | Receptor infrarrojo | entrada `PA0`, 500 ms de muestreo | 400 B | [PDF](reportes/Proyecto%201.pdf) |
| 12 | [`practica-10-teclado-matricial`](practica-10-teclado-matricial/) | Práctica 10 | Teclado matricial 4×4 | barrido de columnas `PC0–PC3` | 380 B | [PDF](reportes/Practica%2010%20Teclado.pdf) |
| 13 | [`practica-11-memoria-eeprom`](practica-11-memoria-eeprom/) | Práctica 11 | Memoria **EEPROM** | `eeprom` + teclado + display | 502 B | [PDF](reportes/Practica%2011.pdf) |
| 14 | [`proyecto-02-bee-bot`](proyecto-02-bee-bot/) | Proyecto 2 | Robot grabador/reproductor (*Bee-Bot*) | 7 pulsadores, buffer de 10 pasos | **700 B** | [DOCX](reportes/Practica%2012.docx) |
| 15 | [`practica-13-convertidor-adc`](practica-13-convertidor-adc/) | Práctica 13 | Convertidor analógico-digital | ADC canal 0, salida `PORTD` | 222 B | [PDF](reportes/Practica%2013.pdf) |
| 16 | [`practica-14-vumetro`](practica-14-vumetro/) | Práctica 14 | Vúmetro de 2 canales | ADC `PA0`/`PA1`, barra/punto/hold | 630 B | [PDF](reportes/Practica%2014.pdf) |
| 17 | [`practica-16-termometro-digital`](practica-16-termometro-digital/) | Práctica 16 | Termómetro digital °C / °F | ADC + 3 dígitos multiplexados | 648 B | — |

**Variantes adicionales** (mismo algoritmo, parámetros distintos):

| Archivo | Diferencia |
|---|---|
| `practica-13-convertidor-adc/P13.c`, `p13-2.c`, `P13.3.c` | Prácticamente idénticos; cambia el reloj (1 MHz / 1 MHz / **8 MHz**) |
| `practica-16-termometro-digital/P16.c` | Vref = **AVcc**, multiplexado `0x01→0x02→0x04`, `delay_ms(1)` |
| `practica-16-termometro-digital/P162.c` | Vref = **AREF**, multiplexado `0x04→0x02→0x01`, `delay_ms(100)` |
| `practica-16-termometro-digital/P163.c` | Vref = **AREF**, `delay_ms(1)` |
| `practica-16-termometro-digital/P164.c` | Vref = **AVcc**, `delay_ms(10)` |
| `proyecto-01-emisor-v1/prueba.c` | Esqueleto vacío (bucle infinito sin contenido) |

## Mapa de proyectos por tema

### Entrada / salida digital

| Práctica | Qué demuestra |
|---|---|
| [01 — Puertos de E/S](practica-01-puertos-e-s/) | `DDRA/B/C/D` como entrada y salida; copiar `PORTB` → `PORTD` bit a bit |
| [02 — Contador asc./desc.](practica-02-contador-asc-desc/) | `PORTC++` y `PORTD--` con temporizado por software |
| [16 — Termómetro digital](practica-16-termometro-digital/) | Multiplexado de 3 dígitos por software + conversión a °C/°F |

### Displays de 7 segmentos

| Práctica | Qué demuestra |
|---|---|
| [03 — BCD → 7 segmentos](practica-03-bcd-a-7-segmentos/) | Tablas `tabla7segmentos` / `tablaHex`; selección decimal (bit 4) / hexadecimal |
| [04 — Contador 0–9](practica-04-contador-0-9/) | Lectura de pulsador + tabla de segmentos; **sin** antirebote |
| [05 / 06 — Flancos y rebotes](practica-05-contador-por-flancos/) | Detección de flancos 1→0 y 0→1 con variable de estado previo |
| [06 — Sin rebotes](practica-06-contador-sin-rebotes/) | Eliminación de rebotes por retardo de 40 ms |
| [07 — Contador 00–99](practica-07-contador-00-99/) | Contador de 2 dígitos con acarreo de unidades a decenas |

### Pulsadores, teclado y memoria

| Práctica | Qué demuestra |
|---|---|
| [07 — Contador 00–99](practica-07-contador-00-99/) | Par de pulsadores como entrada digital con pull-up |
| [08 — Cronómetro 60 s](practica-08-cronometro-60s/) | 3 dígitos, base de tiempo por software, arranque / pausa / reinicio |
| [10 — Teclado matricial](practica-10-teclado-matricial/) | Barrido de teclado 4×4 con decodificación de literales binarios |
| [11 — Memoria EEPROM](practica-11-memoria-eeprom/) | Variable `eeprom` para persistir la última tecla pulsada |

### Conversores analógico-digitales

| Práctica | Qué demuestra |
|---|---|
| [13 — Convertidor ADC](practica-13-convertidor-adc/) | Configuración de `ADMUX`/`ADCSRA`, lectura por *polling* sobre `ADIF` |
| [14 — Vúmetro](practica-14-vumetro/) | Dos canales analógicos, suavizado de lecturas, modos barra / punto / hold |
| [16 — Termómetro digital](practica-16-termometro-digital/) | Conversión ADC → grados Celsius y Fahrenheit |

### Proyectos integradores

| Proyecto | Qué demuestra |
|---|---|
| [**Proyecto 1 — Móvil por infrarrojo**](proyecto-01-emisor-v2/) | Comunicación entre **dos microcontroladores**: emisor con 4 pulsadores y anchos de pulso diferenciados (500 / 700 / 900 / 1100 µs) que codifican 4 direcciones; receptor que mide la duración del pulso y la decodifica |
| [**Proyecto 2 — Bee-Bot**](proyecto-02-bee-bot/) | Robot grabador / reproductor de trayectorias: buffer de 10 pasos en RAM, modos `CLR` / `PAU` / `GO`, antirebote en todos los pulsadores |

## Cómo compilar y grabar

Los proyectos son compatibles con **CodeVisionAVR 3.32** y con **Atmel Studio 7**.
Las rutas dentro de los archivos de proyecto son relativas, así que las carpetas
pueden moverse o renombrarse sin romper nada.

### CodeVisionAVR

1. Abre `CodeVisionAVR`.
2. *Project → Open Project…* y selecciona el archivo `.prj` de la carpeta
   (p. ej. `practica-01-puertos-e-s/P1.prj`).
3. Verifica en *Project → Settings → Compiler* que:
   - **Chip** = `ATmega8535`
   - **Clock frequency** = `1 MHz` (o `8 MHz` según el proyecto — ver
     [`docs/COMPILACION.md`](docs/COMPILACION.md))
4. *Project → Build* (o `F7`).
5. El `.hex` queda en `Debug/Exe/`, el `.eep` en `Debug/Exe/`.

### Atmel Studio

1. *File → Open → Solution…* y selecciona el `.atsln`.
2. Selecciona la configuración **Debug** y compila.
3. Usa el programador desde *Tools → AVR Programming Tools*.

### Línea de comandos (AVRDUDE)

```bash
# Compilar conavr-gcc
avr-gcc -mmcu=atmega8535 -DF_CPU=1000000UL -Os -std=gnu99 \
        -mmcu=atmega8535 -o P1.elf P1.c

# Convertir a Intel HEX
avr-objcopy -O ihex -R .eeprom P1.elf P1.hex

# Grabar en la placa
avrdude -p m8535 -c usbasp -U flash:w:P1.hex:i
```

Guía completa, incluyendo la diferencia entre 1 MHz y 8 MHz:
[`docs/COMPILACION.md`](docs/COMPILACION.md).

## Estructura del repositorio

```
.
├── README.md                    ← este documento
├── LICENSE                      MIT
├── CONTRIBUTING.md
├── CITATION.cff
├── docs/
│   ├── PROYECTOS.md             ficha detallada de cada uno de los 23 firmware
│   ├── HARDWARE.md              ATmega8535, pinout y mapa de uso de puertos
│   ├── COMPILACION.md           CodeVisionAVR, Atmel Studio y AVRDUDE
│   ├── NOTAS-TECNICAS.md        inconsistencias, limitaciones y hallazgos
│   └── GLOSARIO.md              términos usados en los reportes
├── reportes/                    entregables en PDF (y 2 DOCX sin PDF)
├── practica-01-puertos-e-s/
│   ├── P1.c                     código fuente
│   ├── P1.prj                   proyecto CodeVisionAVR
│   ├── P1.atsln                 solución Atmel Studio
│   ├── P1.cproj                 proyecto CodeVisionAVR / Atmel Studio
│   └── README.md                ficha del proyecto
├── ...
└── practica-16-termometro-digital/
```

> [!TIP]
> Los nombres de archivo conservan la nomenclatura original
> (`P1.c`, `P16.c`, `recp.c`…), por lo que el nombre de archivo **no** coincide
> con el de la carpeta. La correspondencia exacta está en
> [`docs/PROYECTOS.md`](docs/PROYECTOS.md).

## Documentación

| Documento | Contenido |
|---|---|
| [`docs/PROYECTOS.md`](docs/PROYECTOS.md) | Ficha de cada firmware: objetivo, principio de funcionamiento, pines, periféricos y observaciones |
| [`docs/HARDWARE.md`](docs/HARDWARE.md) | Descripción del ATmega8535, pinout de 40 pines, tabla de uso de puertos por proyecto y materiales |
| [`docs/COMPILACION.md`](docs/COMPILACION.md) | Configuración de los IDE, frecuencias de reloj, grabación y resolución de problemas |
| [`docs/NOTAS-TECNICAS.md`](docs/NOTAS-TECNICAS.md) | Inconsistencias detectadas, limitaciones conocidas y código muerto |
| [`docs/GLOSARIO.md`](docs/GLOSARIO.md) | Glosario de términos técnicos |

## Reportes

Los entregables de cada práctica están en [`reportes/`](reportes/), con los
nombres originales de entrega:

| Reporte | Práctica / Proyecto | Carpeta de código |
|---|---|---|
| `Practica01 Córdova.pdf` | Práctica 01 — Puertos de E/S | [`practica-01-puertos-e-s/`](practica-01-puertos-e-s/) |
| `Practica02 Cordova.pdf` | Práctica 02 — Contador ascendente y descendente | [`practica-02-contador-asc-desc/`](practica-02-contador-asc-desc/) |
| `Practica03 Cordova.pdf` | Práctica 03 — Convertidor BCD a 7 segmentos | [`practica-03-bcd-a-7-segmentos/`](practica-03-bcd-a-7-segmentos/) |
| `Practica04 Cordova.pdf` | Práctica 04 — Contador de 0 a 9 | [`practica-04-contador-0-9/`](practica-04-contador-0-9/) |
| `Practica05_6 Cordova.pdf` | Prácticas 05 y 06 — Flancos y sin rebotes | [`practica-05-…`](practica-05-contador-por-flancos/), [`practica-06-…`](practica-06-contador-sin-rebotes/) |
| `Practica07 Cordova.pdf` | Práctica 07 — Contador 00–99 | [`practica-07-contador-00-99/`](practica-07-contador-00-99/) |
| `Practica08 Cordova.pdf` | Práctica 08 — Cronómetro de 60 s | [`practica-08-cronometro-60s/`](practica-08-cronometro-60s/) |
| `Proyecto 1.pdf` | Proyecto 1 — Móvil por infrarrojo | [`proyecto-01-emisor-v2/`](proyecto-01-emisor-v2/) + [`proyecto-01-receptor/`](proyecto-01-receptor/) |
| `Practica 10 Teclado.pdf` | Práctica 10 — Teclado matricial | [`practica-10-teclado-matricial/`](practica-10-teclado-matricial/) |
| `Practica 11.pdf` | Práctica 11 — Memoria EEPROM | [`practica-11-memoria-eeprom/`](practica-11-memoria-eeprom/) |
| `Practica 12.docx` | Proyecto 2 — *Bee-Bot* | [`proyecto-02-bee-bot/`](proyecto-02-bee-bot/) |
| `Practica 13.pdf` | Práctica 13 — Convertidor analógico-digital | [`practica-13-convertidor-adc/`](practica-13-convertidor-adc/) |
| `Practica 14.pdf` | Práctica 14 — Vúmetro | [`practica-14-vumetro/`](practica-14-vumetro/) |
| `Practica2.docx` | Práctica de analógico — Puente de Wheatstone + LM741 | *sin firmware asociado* |

**Notas sobre los reportes**

- Sólo se versionan los PDF. Los `.docx` editables se eliminaron cuando existía
  su equivalente en PDF.
- `Practica 12.docx` y `Practica2.docx` se conservan porque **no existe versión
  PDF** de esos documentos.
- El reporte del *Proyecto 2* se titula internamente *«Proyecto 2 — Proyecto 02
  Bee-Bot»* y su archivo de código está en la carpeta
  `proyecto-02-bee-bot/`.
- La Práctica 16 (termómetro digital) **no tiene reporte**: es un ejercicio
  posterior sin entrega formal.

## Limitaciones conocidas

Resumen; el detalle está en [`docs/NOTAS-TECNICAS.md`](docs/NOTAS-TECNICAS.md).

1. **Reloj inconsistente** entre 1 MHz y 8 MHz. Los `.hex` no son intercambiables.
2. **CodeVisionAVR Evaluation** limita el programa a 8 KB. El mayor uso es de
   700 B (8.5 %), así que ningún proyecto quedó limitado por la licencia.
3. **Práctica 04 no tiene antirebote**: el pulsador genera rebotes y el contador
   avanza más de un paso. Es precisamente lo que motivan las prácticas 05 y 06.
4. **Proyecto 2 (Bee-Bot)**: al grabar se guarda `1` para *izquierda* y `2` para
   *derecha*, pero el `switch` de reproducción mapea `1 → DERECHA` y
   `2 → IZQUIERDA`. Posible inversión a verificar.
5. **Práctica 03**: la condición `if (variable >= 16)` nunca se cumple, porque
   `variable` se limita a `PINB & 0x0F` (0–15). Es código muerto.
6. **Discrepancias entre el enunciado y el código** en las prácticas 05/06 y 07
   (el reporte menciona el Puerto B; el código usa `PIND.0`).
7. **No existe la Práctica 15.** La numeración salta de 14 a 16.
8. **`read_adc()`** no limpia explícitamente la bandera `ADIF`.

## Licencia

Distribuido bajo la licencia [MIT](LICENSE).

El código fue escrito con fines educativos durante la materia *Sistemas en
Chip* del IPN-ESCOM. Los reportes originales pertenecen al coursework de la
institución y se incluyen como evidencia de las entregas realizadas.

Si te resulta útil, siéntete libre de usarlo, bifurcarlo (*fork*) o citarlo
citando adecuadamente la fuente.

---

<div align="center">
<sub>
Hecho con CodeVisionAVR y mucho café de laboratorio · IPN — ESCOM
</sub>
</div>