# Compilación y grabación

Los 23 firmware de este repositorio se pueden compilar con **CodeVisionAVR
3.32 Evaluation** (el IDE con el que se hicieron) o con **Atmel Studio 7**, que
es capaz de abrir las soluciones `.atsln` incluidas.

> [!IMPORTANT]
> **La frecuencia de reloj debe coincidir con la de la placa.**
> Las prácticas se compilaron a 1 MHz, salvo tres que quedaron a 8 MHz. Un
> firmware compilado para 1 MHz se ejecuta 8 veces más lento (o se queda
> colgado) en una placa con cristal de 8 MHz, y al revés. La tabla completa está
> en la sección [Frecuencias de reloj por proyecto](#5-frecuencias-de-reloj-por-proyecto).

## 1. Estructura de los archivos de proyecto

Cada carpeta contiene cuatro archivos de proyecto. **Los nombres internos no
coinciden con el nombre de la carpeta**: son los originales del coursework y no
se renombraron para no romper la vinculación con el IDE.

| Archivo | IDE | Qué es |
|---|---|---|
| `P1.prj` | CodeVisionAVR | Proyecto de CodeVisionAVR: chip, reloj, fuses, opciones del compilador |
| `P1.atsln` | Atmel Studio | Solución; referencia al `.cproj` con ruta **relativa** |
| `P1.cproj` | Ambos | Proyecto de código; declara `<Compile Include="P1.c" />` |
| `P1.c` | Ambos | El código fuente |

> Las rutas dentro de `.atsln` y `.cproj` son relativas, así que **las carpetas
> se pueden mover o renombrar libremente** sin romper nada. Esto es lo que
> permitió renombrarlas a `practica-01-puertos-e-s`, etc.

## 2. CodeVisionAVR 3.32

### Abrir un proyecto

1. Inicia **CodeVisionAVR**.
2. *Project → Open Project…*
3. Selecciona el `.prj` de la carpeta, por ejemplo
   `practica-01-puertos-e-s/P1.prj`.

> Si prefieres abrir la solución completa en CodeVisionAVR, usa
> *Project → Open* y selecciona el `.cproj`.

### Configurar chip y reloj

En *Project → Settings → Compiler* comprueba:

| Campo | Valor esperado |
|---|---|
| **Chip** | `ATmega8535` |
| **Clock frequency** | `1000000` (1 MHz) u `8000000` (8 MHz) según la tabla de abajo |
| **Memory model** | `Small` |
| **Optimize for** | `Size` |

### Compilar

- *Project → Build* o `F7`.

Los artefactos se generan en la carpeta `Debug/`:

```
Debug/
├── Exe/     P1.hex   P1.elf   P1.rom    <- lo que se graba
├── List/    P1.lst   P1.map   P1.asm
├── Link/    P1.o     P1.a
└── ...      P1.obj   P1.cof   P1.str1  P1.sym1-3
```

En la práctica 11 se genera además `P11.eep`, con el contenido de la EEPROM.

> **Todos esos archivos están en `.gitignore`.** No se versionan a propósito:
> son artefactos derivados y se regeneran al compilar.

### Limitación de la versión Evaluation

CodeVisionAVR en modo Evaluation compila programas de hasta **8 KB** (8192
bytes). Ninguna de estas prácticas llegó al límite:

| Proyecto | Flash usada | % del microcontrolador |
|---|---|---|
| `proyecto-02-bee-bot` | 700 B | **8.5 %** |
| `practica-16-termometro-digital` | 654 B | 8.0 % |
| `practica-14-vumetro` | 630 B | 7.7 % |
| `practica-11-memoria-eeprom` | 502 B | 6.1 % |

## 3. Atmel Studio 7

1. *File → Open → Solution…* y selecciona el `.atsln`
   (p. ej. `practica-01-puertos-e-s/P1.atsln`).
2. Selecciona la configuración **Debug** en la barra de herramientas.
3. Compila con `F7`.

> **Limitación.** Atmel Studio no reconoce el calificador `eeprom` de
> CodeVisionAVR. La **práctica 11 (memoria EEPROM) no compila tal cual** en
> Atmel Studio: habría que sustituir la declaración por las funciones del
> registro `EECR` de `avr/eeprom.h`. El resto de las prácticas usa C estándar y
> compila sin cambios.

Para grabar, usa *Tools → AVR Programming Tools*.

## 4. Línea de comandos (AVRDUDE)

Compilar con `avr-gcc`, que viene incluido en Atmel Studio y en
[avr-gcc standalone](https://gcc.gnu.org/wiki/avr):

```bash
# 1 MHz  (práctica 01, 02, 03, 04, 05, 06, 07, 08, 10, 11 y los proyectos)
avr-gcc -mmcu=atmega8535 -DF_CPU=1000000UL -Os -std=gnu99 \
        -o P1.elf P1.c

# 8 MHz  (práctica 13 variante P13.3, práctica 14 y práctica 16)
avr-gcc -mmcu=atmega8535 -DF_CPU=8000000UL -Os -std=gnu99 \
        -o P14.elf P14.c

# Convertir a Intel HEX
avr-objcopy -O ihex -R .eeprom P1.elf P1.hex
```

Grabar en la placa:

```bash
# USBasp
avrdude -p m8535 -c usbasp -U flash:w:P1.hex:i

# AVRISP mkII
avrdude -p m8535 -c avr ISP -P 0 -U flash:w:P1.hex:i

# Con ATmega8535 verificado (fábrica)
avrdude -p m8535 -c usbasp -U flash:w:P1.hex:i -V

# Grabar también la EEPROM (solo práctica 11)
avrdude -p m8535 -c usbasp -U flash:w:P1.hex:i -U eeprom:w:P1.eep:i
```

> **`-V` es recomendable en el ATmega8535.** Su firma de fábrica es `0x1E930F`, y
> AVRDUDE avisa si detecta el valor «cero» (`0x0`); usa `-V` para forzar la
> grabación si es tu caso.

## 5. Frecuencias de reloj por proyecto

Extraído de `CPUClock=` en los archivos `.prj`.

| Carpeta | Archivo | Reloj | Cristal en la placa |
|---|---|---|---|
| `practica-01-puertos-e-s` | `P1.c` | 1 MHz | 8 MHz |
| `practica-02-contador-asc-desc` | `P2.c` | 1 MHz | 8 MHz |
| `practica-03-bcd-a-7-segmentos` | `P3.c` | 1 MHz | 8 MHz |
| `practica-04-contador-0-9` | `P4.c` | 1 MHz | 8 MHz |
| `practica-05-contador-por-flancos` | `P5.c` | 1 MHz | 8 MHz |
| `practica-06-contador-sin-rebotes` | `P6.c` | 1 MHz | 8 MHz |
| `practica-07-contador-00-99` | `P7.c` | 1 MHz | 8 MHz |
| `practica-08-cronometro-60s` | `P8.c` | 1 MHz | 8 MHz |
| `proyecto-01-emisor-v1` | `P9.c`, `prueba.c` | 1 MHz | 8 MHz |
| `proyecto-01-emisor-v2` | `p9.c` | 1 MHz | 8 MHz |
| `proyecto-01-receptor` | `recp.c` | 1 MHz | 8 MHz |
| `practica-10-teclado-matricial` | `P10.c` | 1 MHz | 8 MHz |
| `practica-11-memoria-eeprom` | `P11.c` | 1 MHz | 8 MHz |
| `proyecto-02-bee-bot` | `P12.c` | 1 MHz | 8 MHz |
| `practica-13-convertidor-adc` | `P13.c` | 1 MHz | 8 MHz |
| `practica-13-convertidor-adc` | `p13-2.c` | 1 MHz | 8 MHz |
| `practica-13-convertidor-adc` | `P13.3.c` | **8 MHz** | 8 MHz |
| `practica-14-vumetro` | `P14.c` | **8 MHz** | 8 MHz |
| `practica-16-termometro-digital` | `P16.c` | **8 MHz** | 8 MHz |
| `practica-16-termometro-digital` | `P162.c`, `P163.c` | 1 MHz | 8 MHz |
| `practica-16-termometro-digital` | `P164.c` | 1 MHz | 8 MHz |

> **Aviso.** La tabla refleja lo que se compiló, no necesariamente lo que
> funcionó en el laboratorio. Dentro de la práctica 16, `P16.c` quedó a 8 MHz
> mientras `P162.c`, `P163.c` y `P164.c` quedaron a 1 MHz, sin que se
> documente el motivo. Ver [NOTAS-TECNICAS.md](NOTAS-TECNICAS.md).

### Cómo determinar la frecuencia de tu placa

1. Lee el valor grabado en el cristal o en el encapsulado.
2. O mide el período de un parpadeo conocido: si un LED que debería cambiar
   cada 250 ms cambia 8 veces más rápido, tienes 8 MHz y el firmware espera 1 MHz.
3. O compila a 8 MHz y compara: es la opción más simple para estas prácticas.

## 6. Fuses

Las prácticas dejaron los fuses en su configuración por defecto
(`Fuse1`–`Fuse48` a `0` en el `.prj`), es decir, **reloj interno RC**.
El ATmega8535 usa por defecto el RC interno calibrado, y los fuses de reloj
externos (`CKSEL`, `CKOPT`) **no** se tocaron en estas prácticas.

> Si tu placa usa un **cristal externo de 8 MHz**, los fuses de reloj deben estar
> configurados para externo. El proyecto no los modifica, así que:
>
> ```bash
> # Cristal externo, 8 MHz, sin condensador, BOD deshabilitado
> avrdude -p m8535 -c usbasp \
>   -U lfuse:w:0xff:m -U hfuse:w:0xc9:m -U efuse:w:0xff:m
> ```

## 7. Problemas frecuentes

| Síntoma | Causa probable | Solución |
|---|---|---|
| Los retardos salen 8 veces más cortos | Firmware a 1 MHz en placa de 8 MHz | Recompila con `CPUClock = 8000000` |
| Los retardos salen 8 veces más largos | Firmware a 8 MHz en placa de 1 MHz | Recompila con `CPUClock = 1000000` |
| El programa se queda colgado en `read_adc()` | `ADEN` sin activar, o reloj mal configurado | Verifica `ADCSRA` y el reloj |
| El display parpadea o muestra dígitos incompletos | Multiplexado demasiado rápido | Aumenta el retardo entre dígitos (ver práctica 16) |
| El pulsador cuenta más de una vez | Rebote mecánico | Aplica el antirebote de las prácticas 05, 06 y 12 |
| `Error: unable to read address` en AVRDUDE | Programador no conectado o mal orientado | Revisa CLK, MOSI, MISO y RESET, y la alimentación |
| `Signature errors` / `verification error` | Cristal o fuses de reloj incorrectos | Verifica los fuses con `avrdude -p m8535 -c usbasp -v` |
| Atmel Studio no compila la práctica 11 | `eeprom` no es C estándar | Usa CodeVisionAVR, o migra a `avr/eeprom.h` |
| Los `.hex` no aparecen tras compilar | Configuración de salida en `Release` | Compila en modo `Debug`, no `Release` |

## 8. Referencia rápida de comandos

```bash
# Ver el uso de memoria del firmware compilado
avr-size -C --mcu=atmega8535 P1.elf

# Desensamblar para inspección
avr-objdump -S -m avr5 P1.elf > P1.dis

# Ver los símbolos (tamaño de cada variable/función)
avr-nm -S --size-sort P1.elf | tail -20

# Limpiar artefactos antes de recompilar
# (equivale a Project -> Clean en CodeVisionAVR)
```

---

Volver al [índice](../README.md) · Ver [PROYECTOS.md](PROYECTOS.md) ·
[HARDWARE.md](HARDWARE.md) · [NOTAS-TECNICAS.md](NOTAS-TECNICAS.md) ·
[GLOSARIO.md](GLOSARIO.md)
