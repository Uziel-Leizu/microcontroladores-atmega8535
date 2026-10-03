# Fichas de los proyectos

Ficha detallada de cada uno de los **23 archivos fuente** del repositorio: qué
hace el código, qué pines usa y qué conviene tener en cuenta.

Para el mapa de uso de puertos y el pinout del microcontrolador, ver
[HARDWARE.md](HARDWARE.md). Para compilar, ver
[COMPILACION.md](COMPILACION.md).

## índice

| # | Práctica / Proyecto | Archivo | Tema |
|---|---|---|---|
| 1 | [Práctica 01 — Puertos de E/S](#práctica-01--puertos-de-es) | `P1.c` | E/S digital |
| 2 | [Práctica 02 — Contador ascendente y descendente](#práctica-02--contador-ascendente-y-descendente) | `P2.c` | E/S digital |
| 3 | [Práctica 03 — BCD a 7 segmentos](#práctica-03--convertidor-bcd-a-7-segmentos) | `P3.c` | Displays |
| 4 | [Práctica 04 — Contador 0 a 9](#práctica-04--contador-de-0-a-9) | `P4.c` | Displays |
| 5 | [Práctica 05 — Contador por flancos](#práctica-05--contador-activado-por-flancos) | `P5.c` | Pulsadores |
| 6 | [Práctica 06 — Contador sin rebotes](#práctica-06--contador-sin-rebotes) | `P6.c` | Pulsadores |
| 7 | [Práctica 07 — Contador 00 a 99](#práctica-07--contador-de-00-a-99) | `P7.c` | Displays |
| 8 | [Práctica 08 — Cronómetro 60 s](#práctica-08--cronómetro-de-60-segundos) | `P8.c` | Displays + tiempo |
| 9 | [Proyecto 1 — Emisor IR, v1](#proyecto-1--emisor-infrarrojo-v1) | `P9.c` | Comunicación IR |
| 10 | [Proyecto 1 — Emisor IR, v2](#proyecto-1--emisor-infrarrojo-v2) | `p9.c` | Comunicación IR |
| 11 | [Proyecto 1 — Receptor IR](#proyecto-1--receptor-infrarrojo) | `recp.c` | Comunicación IR |
| — | [*prueba* (esqueleto)](#esqueleto-pruebac) | `prueba.c` | — |
| 12 | [Práctica 10 — Teclado matricial](#práctica-10--teclado-matricial) | `P10.c` | Teclado |
| 13 | [Práctica 11 — Memoria EEPROM](#práctica-11--memoria-eeprom) | `P11.c` | EEPROM |
| 14 | [Proyecto 2 — Bee-Bot](#proyecto-2--bee-bot) | `P12.c` | Proyecto final |
| 15 | [Práctica 13 — Convertidor ADC](#práctica-13--convertidor-analógico-digital) | `P13.c` · `p13-2.c` · `P13.3.c` | ADC |
| 16 | [Práctica 14 — Vúmetro](#práctica-14--vúmetro) | `P14.c` | ADC |
| 17 | [Práctica 16 — Termómetro digital](#práctica-16--termómetro-digital) | `P16.c` · `P162.c` · `P163.c` · `P164.c` | ADC + displays |

---

## Práctica 01 — Puertos de E/S

**Carpeta:** [`practica-01-puertos-e-s/`](../practica-01-puertos-e-s/)
**Fuente:** `P1.c` · **Reloj:** 1 MHz · **Flash:** 184 B (2.2 %)

### Objetivo

Programar los puertos como entrada y como salida, y comprobar el
comportamiento básico bidireccional.

### Qué hace

El programa más simple del repositorio: lee `PORTB` completo y lo escribe
íntegro en `PORTD`.

```c
while (1)
      {
      PORTD=PINB;
      }
```

`PINB` es el registro que refleja el **nivel real** de los pines, mientras que
`PORTB` es el registro que define el nivel de salida. Usar `PINB` como fuente es
lo correcto cuando el puerto está configurado como entrada.

### Pines

| Puerto | Configuración | Uso |
|---|---|---|
| `PORTB` | Entrada, pull-ups activos | 8 interruptores o pulsadores |
| `PORTD` | Salida | 8 LEDs |
| `PORTA`, `PORTC` | Entrada, sin uso | — |

`PORTB` tiene los pull-ups internos activados (`PORTB = 0xFF`), de modo que un
pulsador entre `PBn` y `GND` se lee como `0` al presionarse y `1` en reposo.

### Observaciones

- Sin retardo ni antirebote: el LED refleja el estado instantáneo del
  pulsador, incluyendo el rebote mecánico. En esta práctica eso es lo esperado.
- El cátodo común y el ánodo común aún no aparecen; el LED de `PORTD` se
  conecta directamente con una resistencia en serie.

---

## Práctica 02 — Contador ascendente y descendente

**Carpeta:** [`practica-02-contador-asc-desc/`](../practica-02-contador-asc-desc/)
**Fuente:** `P2.c` · **Reloj:** 1 MHz · **Flash:** 220 B (2.7 %)

### Objetivo

Realizar un contador ascendente y uno descendente, de forma independiente, con
temporizado por software.

### Qué hace

```c
while (1)
      {
      PORTC++;
      PORTD--;
      delay_ms(250);
      }
```

`PORTC` recorre los valores `0x00` → `0xFF` → `0x00` y `PORTD` hace lo mismo
pero a la inversa. Como el puerto es de 8 bits, el desbordamiento se produce de
forma natural: no hay Modular en ningún punto porque el tipo `unsigned char`
hace el acarreo solo.

### Pines

| Puerto | Configuración | Uso |
|---|---|---|
| `PORTC` | Salida | 8 LEDs — cuenta ascendente |
| `PORTD` | Salida | 8 LEDs — cuenta descendente |
| `PORTA` | Salida, sin uso | — |
| `PORTB` | Entrada con pull-ups, sin uso | — |

### Observaciones

- El retardo es **bloqueante**: 250 ms por iteración, así que el programa no
  comprueba nada más mientras espera.
- La lighted visual depende de cómo se conecte cada LED (resistencia en serie
  hacia `GND` o hacia `+5 V`). El enunciato pedía dos grupos independientes.

---

## Práctica 03 — Convertidor BCD a 7 segmentos

**Carpeta:** [`practica-03-bcd-a-7-segmentos/`](../practica-03-bcd-a-7-segmentos/)
**Fuente:** `P3.c` · **Reloj:** 1 MHz · **Flash:** 314 B (3.8 %)

### Objetivo

Realizar un contador BCD empleando un display de 7 segmentos, con selección
entre modo decimal y hexadecimal.

### Qué hace

El nibble bajo de `PORTB` contiene el valor que se quiere mostrar y el bit 4
actúa como selector de modo:

```c
variable = PINB & 0x0f;   // valor a mostrar (0-15)
selector = PINB & 0x10;   // 0x10 = decimal, 0x00 = hexadecimal

if (selector == 16) {                       // modo decimal
  if (variable < 10) { /* tabla7segmentos */ }
  if (variable >= 10) { /* muestra 'E' (0x79) */ }
} else {                                     // modo hexadecimal
  if (variable < 16) { /* tablaHex */ }
  if (variable >= 16) { /* muestra 'E' */ }
}
```

La clave del ejercicio es que **decimal y hexadecimal no comparten tabla**:
`tabla7segmentos` solo tiene 10 entradas, así que un valor de 10 a 15 se
muestra como `'E'` (error) en modo decimal, mientras que en modo hexadecimal sí
se representa con `A`–`F`.

### Tablas de conversión

```c
const char tabla7segmentos [10] = {0x3f,0x06,0x5b,0x4f,0x66,
                                   0x6d,0x7d,0x07,0x7f,0x6f};
const char tablaHex [16]       = {0x3f,0x06,0x5b,0x4f,0x66,0x6d,
                                   0x7d,0x07,0x7f,0x6f,
                                   0x77,0x7c,0x39,0x5e,0x79,0x71};
```

### Pines

| Puerto | Configuración | Uso |
|---|---|---|
| `PORTB` | Entrada con pull-ups | `PB0`–`PB3` = valor, `PB4` = selector |
| `PORTC` | Salida | display cátodo común |
| `PORTA` | Salida | display ánodo común (`~valor`) |

### Observaciones

- **`variable >= 16` es código muerto.** `variable` se define como
  `PINB & 0x0F`, así que su rango es 0–15 y la condición nunca se cumple. Aparece
  en las dos ramas (decimal y hexadecimal) por simetría con la copia de otro
  ejercicio.
- El enunciado pedía un *contador*, pero la implementación final lee un valor fijo
  del puerto en lugar de generarlo internamente. Es la lectura literal del
  ejercicio: el microcontrolador hace de conversor, no de contador.

---

## Práctica 04 — Contador de 0 a 9

**Carpeta:** [`practica-04-contador-0-9/`](../practica-04-contador-0-9/)
**Fuente:** `P4.c` · **Reloj:** 1 MHz · **Flash:** 228 B (2.8 %)

### Objetivo

Realizar un contador de 0 a 9 mostrado en un display, activado con un pulsador.

### Qué hace

```c
while (1){
  if (boton == 0)      // activo en 0 gracias al pull-up
  var1++;
  if (var1 == 10)
  var1 = 0;
  PORTC  = tabla7segmentos[var1];
  PORTA  = ~(tabla7segmentos[var1]);
}
```

Un pulsador en `PB0` incrementa el contador módulo 10 y el resultado se muestra
en los dos displays.

### Observaciones

- **No hay antirebote.** El bucle `while(1)` se ejecuta miles de veces por
  segundo, así que un solo clic genera varios flancos y el contador avanza más de
  un paso. Esto es exactamente el problema que motivan las prácticas 05 y 06, y
  el propio reporte de la práctica 05 lo menciona como conclusión.
- `if (boton == 0) var1++;` sin llaves es válido en C, pero el increment queda
  condicionado de forma poco evidente a quien lea el código.

---

## Práctica 05 — Contador activado por flancos

**Carpeta:** [`practica-05-contador-por-flancos/`](../practica-05-contador-por-flancos/)
**Fuente:** `P5.c` · **Reloj:** 1 MHz · **Flash:** 298 B (3.6 %)

### Objetivo

Contar los flancos de un pulsador para que cada pulsación incremente el
contador **una sola vez**.

### Qué hace

La técnica es **detección de flanco por comparación de estados**. Se guarda el
estado anterior del pulsador y se compara con el actual:

```c
if (boton == 0) botona = 0;
else            botona = 1;

if ((botona == 0) && (botonp == 1))   // hubo transición de 1 a 0
  var++;

botonp = botona;                        // guarda el estado para la próxima vuelta
```

`botona` es el estado actual; `botonp` (*botón previo*) es el de la iteración
anterior. La cuenta sólo ocurre en la transición **1 → 0**, que es el flanco de
pulsación de un pulsador activo en bajo.

### Observaciones

- **Todavía no elimina el rebote.** Detecta el flanco correctamente, pero los
  rebotes mecánicos también son flancos 1 → 0. Es el puente lógico hacia la
  práctica 06.
- Las dos últimas variables se inicializan a 0 por ser globales, así que el
  primer clic puede no registrarse hasta que el pulsador esté en un estado conocido.
- El enunciado de la práctica menciona el Puerto B y el código usa `PINB.0`,
  coherente; la descripción del material del reporte menciona el Puerto D, que
  no corresponde a esta versión del código.

---

## Práctica 06 — Contador sin rebotes

**Carpeta:** [`practica-06-contador-sin-rebotes/`](../practica-06-contador-sin-rebotes/)
**Fuente:** `P6.c` · **Reloj:** 1 MHz · **Flash:** 340 B (4.2 %)

### Objetivo

Eliminar el rebote de un pulsador para que cada pulsación cuente exactamente
una vez.

### Qué hace

Es la práctica 05 más un retardo de bloqueo en cada flanco:

```c
if ((botonp == 1) && (botona == 0)) {   // flanco de pulsación
  var++;
  if (var == 10) var = 0;
  delay_ms(40);                        // espera a que termine el rebote
}
if ((botonp == 0) && (botona == 1))     // flanco de liberación
  delay_ms(40);
```

El retardo se aplica **en ambos flancos** para que el pulsador quede estable
antes de volver a muestrearlo.

### Observaciones

- `delay_ms(40)` es un valor empírico: debe ser mayor que la duración del
  rebote (típicamente 5–20 ms en pulsadores mecánicos) y menor que el tiempo
  entre pulsaciones consecutivas.
- El precio es **bloqueante**: durante 40 ms el microcontrolador no hace nada
  más. En una aplicación real lo correcto sería un temporizador o una
  interrupción. En el ATmega8535 el objetivo de la materia es prácticas, y el
 blink de un display no se resiente.
- Es la solución canónica de rebote por software; la alternativa es un circuito
  RC con un disparador Schmitt.

---

## Práctica 07 — Contador de 00 a 99

**Carpeta:** [`practica-07-contador-00-99/`](../practica-07-contador-00-99/)
**Fuente:** `P7.c` · **Reloj:** 1 MHz · **Flash:** 350 B (4.3 %)

### Objetivo

Contador de 00 a 99 activado con un par infrarrojo, sobre dos displays.

### Qué hace

Extiende la técnica de la práctica 06 a **dos dígitos**, con acarreo de unidades
a decenas:

```c
if ((botonp == 1) && (botona == 0)) {
  un++;
  if (un == 10) {        // acarreo
    un = 0;
    dec++;
    if (dec == 10) dec = 0;
  }
  delay_ms(40);
}
PORTC = tabla7segmentos[un];    // unidades
PORTA = ~(tabla7segmentos[dec]); // decenas
```

### Observaciones

- El pulsador se lee de **`PIND.0`** (`#define boton PIND.0`) y `DDRD` tiene ese
  bit como entrada mientras los otros 7 son salida. Es la única práctica que
  reaprovecha `PORTD` con una función mixta.
- El enunciado original titula la práctica *"Contador de 00 a 99 activado con
  infrarrojo"* y el material incluye un par emisor/receptor infrarrojo con
  transistor 2N2222. El código, sin embargo, lee un **pulsador**: la versión
  entregada usa el mismo esquema que la práctica 06 con dos dígitos. La
  diferencia entre el enunciado y el código está anotada en
  [NOTAS-TECNICAS.md](NOTAS-TECNICAS.md).

---

## Práctica 08 — Cronómetro de 60 segundos

**Carpeta:** [`practica-08-cronometro-60s/`](../practica-08-cronometro-60s/)
**Fuente:** `P8.c` · **Reloj:** 1 MHz · **Flash:** 372 B (4.5 %)

### Objetivo

Realizar un cronómetro de 59.9 segundos sobre tres displays de 7 segmentos.

### Qué hace

Tres pulsadores en los bits bajos de `PORTB` controlan el cronómetro, y tres
displays muestran décimas, segundos y —aunque el enunciado pide 60 s— el
contador de decenas se detiene en 6.

```c
var = PINB & 0x07;         // lectura de los 3 pulsadores

if (var == 3) bandera = 1;  // arrancar
if (var == 5) bandera = 0;  // pausar
if (var == 6) { bandera = 0; un = dec = ms = 0; }  // reiniciar

if (bandera == 1) {
  ms++;
  if (ms == 10) {          // 10 décimas = 1 segundo
    ms = 0;
    un++;
    if (un == 10) {
      un = 0;
      dec++;
      if (dec == 6) dec = 0;   // se detiene en 60 s
    }
  }
}

PORTC = tabla7segmentos[un];
PORTA = ~(tabla7segmentos[dec]);
PORTD = tabla7segmentos[ms];
delay_ms(100);
```

### Observaciones

- El tiempo no se mide con un **timer de hardware** sino por software: la
  comentario `delay_ms(100)` del bucle es la referencia temporal del conjunto, y
  cada 10 vueltas de `ms` se avanza un segundo. Es una aproximación: acumula
  error de temporización de la Crystal y del retardo.
- Los tres displays **no están multiplexados**: se mantienen los tres encendidos
  a la vez, cada uno en su puerto completo. El multiplexado por software aparece
  por primera vez en la práctica 16.
- Los valores de los tres pulsadores se leen como un número de 3 bits
  (`0b011` = arrancar, `0b101` = pausar, `0b110` = reiniciar), no como tres
  banderas independientes.

---

## Proyecto 1 — Emisor infrarrojo v1

**Carpeta:** [`proyecto-01-emisor-v1/`](../proyecto-01-emisor-v1/)
**Fuente:** `P9.c` · **Reloj:** 1 MHz · **Flash:** 286 B (3.5 %)

### Objetivo del proyecto

Diseñar un móvil controlado por infrarrojo, con movimientos adelante y atrás
según las flechas. El proyecto usa **dos microcontroladores**: uno emite y otro
recibe.

### Qué hace

Cuatro pulsadores en `PORTD` codifican cuatro direcciones. La comunicación no
usa protocolo: **el ancho del pulso es el dato**.

```c
#define IZQ   PIND.0
#define DER   PIND.1
#define ARRIBA PIND.2
#define ABAJO PIND.3

if (IZQ == 0) {          // 25 % de ciclo
  PORTA = 0xff; delay_ms(25);  PORTA = 0x00; delay_ms(475);
} else if (DER == 0) {   // 50 %
  PORTA = 0xff; delay_ms(50);  PORTA = 0x00; delay_ms(450);
} else if (ARRIBA == 0) { // 75 %
  PORTA = 0xff; delay_ms(75);  PORTA = 0x00; delay_ms(425);
} else if (ABAJO == 0) {  // 100 %
  PORTA = 0xff; delay_ms(100); PORTA = 0x00; delay_ms(400);
}
```

El periodo total es siempre **500 ms**; lo que cambia es el tiempo en alto. El
receptor mide cuánto tiempo estuvo activa la señal y de ahí deduce la
dirección.

### Observaciones

- Los pulsadores se prueban con `if` encadenados (`if` / `else if`), así que
  **solo se atiende una dirección a la vez**. Si se pulsan dos simultáneamente
  gana la primera de la lista.
- No hay antirebote. El receptor mide durante 500 ms seguidos, lo que hace que
  un rebote no llegue a alterar la lectura final, pero durante la transición sí
  puede generar pulsos espurios.
- Esta es la **versión 1**: la anchura del pulso es un ciclo de trabajo continuo,
  no un pulso estrecho. La versión 2 (siguiente) lo cambia.

---

## Proyecto 1 — Emisor infrarrojo v2

**Carpeta:** [`proyecto-01-emisor-v2/`](../proyecto-01-emisor-v2/)
**Fuente:** `p9.c` · **Reloj:** 1 MHz · **Flash:** 290 B (3.5 %)

### Qué cambia respecto a la v1

El emisor pasa de emitir un **tren de pulsos** a emitir un **pulso único** de
duración codificada:

```c
if (boton1 == 0){      PORTA = 0x01;
                     delay_us(500);   PORTA = 0x00;  delay_us(500); }
else if (boton2 == 0){ PORTA = 0x01;
                     delay_us(700);   PORTA = 0x00;  delay_us(700); }
else if (boton3 == 0){ PORTA = 0x01;
                     delay_us(900);   PORTA = 0x00;  delay_us(900); }
else if (boton4 == 0){ PORTA = 0x01;
                     delay_us(1100);  PORTA = 0x00;  delay_us(1100); }
else { PORTA = 0x00; }
```

Cuatro duraciones distintas — **500, 700, 900 y 1100 µs** — codifican las cuatro
direcciones. Sólo se excita `PORTA.0`, que es la línea conectada al LED
emisor.

### Observaciones

- Los retardos están en **microsegundos**, no en milisegundos. A 1 MHz,
  `delay_us(500)` es perfectamente resoluble; el cambio de unidades es la
  diferencia funcional clave de esta versión.
- Al no haber ciclo activo continuo, el LED emisor consume mucho menos y la
  señal es más limpia para el receptor.
- La inicialización generada deja `PB4` como entrada y el resto de `PORTB` como
  salida, aunque el código no usa `PORTB`. Es un resto de la configuración del
  receptor, de la que este archivo se copió.

---

## Proyecto 1 — Receptor infrarrojo

**Carpeta:** [`proyecto-01-receptor/`](../proyecto-01-receptor/)
**Fuente:** `recp.c` · **Reloj:** 1 MHz · **Flash:** 400 B (4.9 %)

### Qué hace

Es el otro microcontrolador del Proyecto 1. Recibe la señal en `PA0` y mide
cuánto tiempo permanece en alto durante una ventana de 500 ms:

```c
#define ENTRADA PINA.0
#define ARRIBA 0x0A
#define ABAJO  0x05
#define IZQ    0x02
#define DER    0x08

contador = 0;
for (i = 0; i < 500; i++) {
  if (ENTRADA == 1) contador++;
  delay_ms(1);
}

if      (contador >= 15  && contador <= 35)  PORTC = ARRIBA;
else if (contador >= 40  && contador <= 60)  PORTC = IZQ;
else if (contador >= 65  && contador <= 85)  PORTC = DER;
else if (contador >= 90  && contador <= 110) PORTC = ABAJO;
else                                       PORTC = 0x00;
```

La ventana de muestreo es de **500 iteraciones de 1 ms**, y cada intervalo de
conteo se asocia a una dirección. Los valores esperados coinciden con las
duraciones del emisor v2 (500/700/900/1100 µs) redondeadas a milisegundos.

### Los patrones de `PORTC`

Los cuatro patrones no son dígitos: son **LEDs individuales** repartidos en los
8 bits del puerto.

| Dirección | Patrón | Binario | LEDs encendidas |
|---|---|---|---|
| `ARRIBA` | `0x0A` | `00001010` | bits 3 y 1 |
| `ABAJO` | `0x05` | `00000101` | bits 2 y 0 |
| `IZQ` | `0x02` | `00000010` | bit 1 |
| `DER` | `0x08` | `00001000` | bit 3 |

### Observaciones

- Los intervalos dejan **huecos** (36–39, 61–64, 86–89) y se solapan en la
  frontera (90–110). Un valor en esos Huecos no dispara ninguna acción, lo que
  hace la decodificación más robusta frente a ruido, a costa de descartar
  entradas válidas en los bordes.
- La medición es de **milisegundos**, mucho más burda que los microsegundos del
  emisor: por eso hay rangos amplios en lugar de valores exactos.
- Es el único proyecto cuya lógica **no** cabe en `while(1)` con `if`: usa un
  `for` anidado con retardo de 1 ms, que bloquea el microcontrolador durante
  medio segundo en cada ciclo.

---

## Esqueleto `prueba.c`

**Carpeta:** [`proyecto-01-emisor-v1/`](../proyecto-01-emisor-v1/)
**Fuente:** `prueba.c` · **Flash:** 178 B (2.2 %)

Proyecto generado por CodeWizard y **nunca implementado**: el `while(1)` está
vacío. Se conserva porque forma parte del desarrollo del Proyecto 1 (sirvió
como andamiaje antes de escribir `P9.c`).

Los 178 B corresponden al esqueleto de inicialización que CodeWizard genera
—puertos, timers, USART, ADC, SPI, TWI— más el bucle infinito vacío.

---

## Práctica 10 — Teclado matricial

**Carpeta:** [`practica-10-teclado-matricial/`](../practica-10-teclado-matricial/)
**Fuente:** `P10.c` · **Reloj:** 1 MHz · **Flash:** 380 B (4.6 %)

### Objetivo

Realizar un teclado matricial y mostrar la tecla pulsada en un display.

### Qué hace

Un teclado 4×4 tiene 16 teclas y 8 líneas, así que se recorre por **barrido**:
se activa una columna y se leen las cuatro filas.

```c
PORTC = 0b11111110;          // columna 0 a 0 (activa en bajo)
lectura = PINC & 0b11110000; // lee las 4 filas

if (lectura == 0b11100000) tecla = 10;   // A
if (lectura == 0b11010000) tecla = 11;   // B
if (lectura == 0b10110000) tecla = 12;   // C
if (lectura == 0b01110000) tecla = 13;   // D
```

El patrón `0b1110xxxx` indica *qué fila* está presionada, porque al haber
pull-ups la fila presionada cae a 0. Se repite para las cuatro columnas y al
final se muestra el resultado:

```c
PORTD = tablaHex[tecla];
```

### Mapa de teclas

La distribución es la estándar de un teclado de calculadora:

| | Col. 0 | Col. 1 | Col. 2 | Col. 3 |
|---|---|---|---|---|
| **Fila 0** | `1` | `2` | `3` | `A` (10) |
| **Fila 1** | `4` | `5` | `6` | `B` (11) |
| **Fila 2** | `7` | `8` | `9` | `C` (12) |
| **Fila 3** | `*` (14) | `0` | `#` (15) | `D` (13) |

### Pines

| Puerto | Configuración | Uso |
|---|---|---|
| `PORTC` | `PC0`–`PC3` salida, `PC4`–`PC7` entrada | teclado 4×4 |
| `PORTD` | Salida | display |
| `PORTA`, `PORTB` | Salida, sin uso | — |

### Observaciones

- **Los literales binarios** (`0b11111110`, `0b11110000`) son una extensión de
  C99/GNU y CodeVisionAVR los acepta. Hacen el código mucho más legible que
  escribir `0xFE` y `0xF0`.
- El barrido **no es bloqueante ni temporizado**: las cuatro columnas se leen
  seguidas, sin `delay`. En la práctica eso funciona porque el barrido completo
  tarda menos que el tiempo que un dedo tarda en pulsar y soltar.
- No hay antirebote, y `tecla` conserva el último valor leído, así que la
  última tecla permanece en el display hasta que se pulse otra.
- Si no hay ninguna tecla pulsada, `lectura` vale `0b11110000`, que no
  coincide con ningún patrón, y `tecla` conserva el valor anterior.

---

## Práctica 11 — Memoria EEPROM

**Carpeta:** [`practica-11-memoria-eeprom/`](../practica-11-memoria-eeprom/)
**Fuente:** `P11.c` · **Reloj:** 1 MHz · **Flash:** 502 B (6.1 %)

### Objetivo

Hacer uso de la memoria EEPROM del microcontrolador.

### Qué hace

Es la práctica 10 más persistencia. El añadido es una sola línea:

```c
eeprom char datoguardar;    // 1 byte en EEPROM
void checaBoton(void);      // declarada pero nunca definida ni llamada
```

```c
void main(void)
{
tecla = datoguardar;        // recupera el valor al arrancar

while (1)
{
  /* ... barrido del teclado idéntico a la práctica 10 ... */

  if (boton_guarda == 0)    // pulsador en PA0
      datoguardar = tecla;  // guarda la última tecla

  PORTD = tablaHex[tecla];
}
```

### Cómo funciona la EEPROM en AVR

CodeVisionAVR extiende el lenguaje C con el calificador `eeprom`:

```c
eeprom char datoguardar;
```

Es una **extensión del compilador**, no C estándar: la variable se ubica en el
espacio de direcciones de EEPROM en lugar de SRAM. Para leer y escribir desde C normal
harían falta las funciones del registro `EECR`, que aquí no se usan.

El Atmega8535 tiene **4 KB de EEPROM** y esta práctica sólo
aprovecha **1 byte** (`EECR` → 512 bytes libre, y el archivo `.eep` generado
refleja ese único byte).

### Observaciones

- `tecla = datoguardar;` está **fuera** del bucle: se ejecuta una sola vez al
  arrancar. Así es como se recupera el valor guardado tras un reset o un
  apagado.
- La escritura es inmediata en cada pulsación de `boton_guarda`. La EEPROM del
  AVR tiene un límite de ciclos de escritura por celda (típicamente 100 000); con
  uso normal de teclado el margen es enorme.
- **`checaBoton(void)` queda declarada sin definir.** Es un resto del
  desarrollo: el control de rebote del pulsador se escribiría ahí, pero la
  versión final lo hizo en línea. El compilador no se queja porque la función
  nunca se llama, y la_optimización la elimina.
- Es el **único** proyecto del repositorio que genera un archivo `.eep`.
- El enunciado de esta práctica y el de la práctica 12 comparten texto sobre la
  EEPROM; el código de la 12 no la usa.

---

## Proyecto 2 — Bee-Bot

**Carpeta:** [`proyecto-02-bee-bot/`](../proyecto-02-bee-bot/)
**Fuente:** `P12.c` · **Reloj:** 1 MHz · **Flash:** 700 B (8.5 %)

### Objetivo del proyecto

Robot grabador y reproductor de trayectorias: el usuario teaches una secuencia
de movimientos y luego el robot la repite. Es el **proyecto más complejo** del
repositorio y el mayor de todos en consumo de memoria.

### Qué hace

**Fase de grabación** (`mode == 0`). Cuatro pulsadores de dirección escriben un
código en un buffer de 10 pasos:

```c
unsigned char inst[size];          // size = 10
unsigned char write_index = 0, read_index = 0;

if (p_izq == 1 && a_izq == 0) { inst[write_index] = 1; write_index++; }
if (p_der == 1 && a_der == 0) { inst[write_index] = 2; write_index++; }
if (p_arr == 1 && a_arr == 0) { inst[write_index] = 3; write_index++; }
if (p_aba == 1 && a_aba == 0) { inst[write_index] = 4; write_index++; }
```

**Fase de reproducción** (`mode == 1`). Un `switch` ejecuta cada paso guardado:

```c
current = inst[read_index];
read_index++;
switch (current) {
  case 1: PORTC = DERE;  delay_ms(500); PORTC = 0x00; break;
  case 2: PORTC = IZQI;  delay_ms(500); PORTC = 0x00; break;
  case 3: PORTC = ARRIBA; delay_ms(500); PORTC = 0x00; break;
  case 4: PORTC = ABAJO; delay_ms(500); PORTC = 0x00; break;
}
```

**Control de modo**, con los tres pulsadores adicionales:

| Pulsador | Patilla | Efecto |
|---|---|---|
| `CLR` | `PA4` | Borra el buffer, reinicia los índices, vuelve a `mode = 0` |
| `PAU` | `PA5` | Vuelve a `mode = 0` (abandona la reproducción) |
| `GO` | `PA6` | Pone `mode = 1` (empieza a reproducir) |

### Los siete pulsadores y el antirebote

```c
bit a_izq, a_der, a_arr, a_aba, a_clr, a_pau, a_go,   // estado actual
    p_izq, p_der, p_arr, p_aba, p_clr, p_pau, p_go;   // estado previo
```

Todos siguen el mismo patrón de las prácticas 05 y 06: comparación de estado
actual contra previo, y `delay_ms(20)` en **ambos** flancos. Además, la
actualización de las variables `p_*` está **al final** del bucle, lo que
centraliza la lógica de antirebote:

```c
p_izq = a_izq;  p_der = a_der;  p_arr = a_arr;  p_aba = a_aba;
p_clr = a_clr;  p_pau = a_pau;  p_go  = a_go;
```

### Los patrones de `PORTC`

Igual que en el receptor del Proyecto 1, `PORTC` no muestra dígitos sino que
controla LEDs de dirección:

| Dirección | Patrón | Binario |
|---|---|---|
| `ARRIBA` | `0x0A` | `00001010` |
| `ABAJO` | `0x05` | `00000101` |
| `IZQ` (`IZQI`) | `0x02` | `00000010` |
| `DER` (`DERE`) | `0x08` | `00001000` |

### Observaciones

- **Posible inversión izquierda/derecha.** Al grabar, `IZQ` escribe `1` y `DER`
  escribe `2`, pero el `switch` reproduce `1 → DERE` y `2 → IZQI`. Tal como está,
  una trayectoria grabada giraría al revés. Los comentarios del `switch`
  (`// Derecha`, `// Izquierda`) no coinciden con el orden de grabación. Ver
  [NOTAS-TECNICAS.md](NOTAS-TECNICAS.md).
- El buffer está en **SRAM**, no en EEPROM: la trayectoria se pierde al apagar
  el robot. `size = 10` es el máximo de pasos grabables.
- Cuando el buffer se llena (`write_index == size`), `mode` deja de pasar a la
  rama de grabación y el robot deja de responder a las direcciones hasta que se
  pulse `CLR`.
- La reproducción **no es reiniciable**: `read_index` sólo avanza, y al llegar a
  `size` la rama de reproducción deja de ejecutarse. Hay que pulsar `CLR` para
  volver a grabar.
- `PORTC = 0x00` tras cada paso de 500 ms apaga los LEDs durante la transición.
- La estructura de `if` anidados con variables globales `bit` es más larga que
  el resto del repositorio porque aquí hay **siete** entradas que filtrar.

---

## Práctica 13 — Convertidor analógico-digital

**Carpeta:** [`practica-13-convertidor-adc/`](../practica-13-convertidor-adc/)
**Fuentes:** `P13.c`, `p13-2.c`, `P13.3.c` · **Flash:** 222 B (2.7 %) c/u

### Objetivo

Usar el convertidor analógico-digital del microcontrolador.

### Qué hace

Lee el canal 0 del ADC y escribe el resultado directo en `PORTD`:

```c
#define ADC_VREF_TYPE ((0<<REFS1) | (1<<REFS0) | (1<<ADLAR))

unsigned char read_adc(unsigned char adc_input)
{
  ADMUX = adc_input | ADC_VREF_TYPE;
  delay_us(10);
  while ((ADCSRA & (1<<ADIF)) == 0);   // espera a que termine la conversión
  return ADCH;                          // 8 MSB, porque ADLAR = 1
}

void main(void)
{
  ADMUX = ADC_VREF_TYPE;
  while (1)
  {
    x = read_adc(0);
    PORTD = x;
  }
}
```

### Configuración del ADC

| Registro | Valor | Significado |
|---|---|---|
| `ADEN` | 1 | ADC habilitado |
| `ADPS2..0` | `010` | Preescalador ÷64 |
| `REFS1..0` | `01` | Referencia externa en `AREF` |
| `ADLAR` | 1 | Resultado justificado a la izquierda |

Con `ADLAR = 1`, `ADCH` contiene los 8 bits altos de la conversión de 10 bits,
así que el rango devuelto es 0–255 y `PORTD` lo muestra en binario sobre 8 LEDs.

### Observaciones

- **Las tres variantes son esencialmente idénticas.** La única diferencia real
  es la frecuencia de reloj declarada en el proyecto: `P13.c` y `p13-2.c` están
  configurados a **1 MHz**, y `P13.3.c` a **8 MHz**. El código fuente es el mismo
  salvo por el espaciado de la llamada `read_adc(0)`. Se conservan los tres
  porque son compilaciones que se hicieron en momentos distintos del
  coursework.
- La lectura es **por consulta (polling)**: el bucle espera mirando `ADIF`. No
  hay interrupción, y por eso `ADIE` permanece en 0.
- `delay_us(10)` tras escribir `ADMUX` es el tiempo de (*settling*) del
  convertidor antes de iniciar una conversión.
- Con preescalador ÷64 a 1 MHz, una conversión dura unos 125 µs. El
  `delay_ms` implícito no hace falta porque la espera es activa.
- La función `read_adc()` **no limpia `ADIF`**. Ver
  [NOTAS-TECNICAS.md](NOTAS-TECNICAS.md).

---

## Práctica 14 — Vúmetro

**Carpeta:** [`practica-14-vumetro/`](../practica-14-vumetro/)
**Fuente:** `P14.c` · **Reloj:** 8 MHz · **Flash:** 630 B (7.7 %)

### Objetivo

Usar el convertidor analógico-digital implementándolo como un **vúmetro de dos
canales**.

### Qué hace

Dos señales analógicas (`PA0` y `PA1`) se muestrean y se muestran como barras de
LEDs, con suavizado de las lecturas:

```c
valor = read_adc(0);
if (valor == anterior_a) {
    if (cantidad_a < 8) cantidad_a++;   // lleva 8 lecturas iguales seguidas
} else
    cantidad_a = 0;                     // cambió: reinicia el contador
intensidad = calcular_intensidad(valor, cantidad_a, modo);
PORTC = intensidad;
```

`calcular_intensidad()` decide cuántos bits enciende y de qué forma:

```c
unsigned char calcular_intensidad(unsigned char valor,
                                  unsigned char cantidad, char modo)
{
   char num_de_unos = valor / 31;         // 0..8 para un valor de 0..255
   unsigned char intensidad = 0;
   num_de_unos = (cantidad < num_de_unos) ? (num_de_unos - cantidad) : 0;
   for (i = 0; i < num_de_unos; i++) {
     if (modo == 0) {            // barra: 0x01, 0x03, 0x07, 0x0F...
         if (i == 0) intensidad = (intensidad << 1) | 0x01;
         else        intensidad = intensidad << 1;
     } else {                     // punto: todo en 1
         intensidad = (intensidad << 1) | 0x01;
     }
   }
   return intensidad;
}
```

### Los tres modos

| Dispositivo | `modo` | Efecto |
|---|---|---|
| — | `0` | **Barra** — enciende los LEDs adyacentes desde el origen (`0x01`, `0x03`, `0x07`…) |
| `btn_punto` (`PB2`) | `1` | **Punto** — enciende todos los LEDs del segmento (`0x01`, `0x03`, `0x07`, `0x0F`…) |
| `btn_hold` (`PB1`) | `2` | **Hold** — congela la lectura en el valor más alto alcanzado |

El modo `2` se distingue en la práctica porque `calcular_intensidad()` trata
`modo != 0` con la misma rama: en realidad **hold y punto se comportan igual**,
y la retención del valor máximo depende del hardware o de la repetición del ADC, no del código.

### El suavizado

La idea es que la barra no parpadee por ruido. `cantidad` cuenta cuántas
lecturas **consecutivas idénticas** lleva la señal; sólo cuando supera el valor
de la barra se atenúa la intensidad (`num_de_unos - cantidad`). Así una señal
ruidosa nunca llega a los niveles altos.

### Observaciones

- `valor / 31` divide 255 entre 8 tramos: por eso una barra de este código tiene
  **8 LEDs**.
- `read_adc()` se reutiliza de la práctica 13, pero con la referencia cambiada
  a **AVcc** (`REFS1..0 = 00`) y preescalador **÷128**.
- La variable `saltar` funciona como un **divisor de iteración**: si está activa,
  el bucle hace `continue` y se salta la lectura de ese ciclo. Como se alterna
  al principio y al final, en la práctica **sólo se procesa la mitad de las
  vueltas**.
- La granularidad del suavizado está limitada a 8 (`cantidad_a < 8`), lo que
  restringe el rango de `num_de_unos` a 0–8 justo en el máximo.
- Es uno de los dos proyectos compilados a **8 MHz**.

---

## Práctica 16 — Termómetro digital

**Carpeta:** [`practica-16-termometro-digital/`](../practica-16-termometro-digital/)
**Fuentes:** `P16.c`, `P162.c`, `P163.c`, `P164.c`
**Reloj:** 8 MHz · **Flash:** 648–654 B (7.9–8.0 %)

### Objetivo

Termómetro digital que muestra la temperatura en **Celsius o Fahrenheit**,
alternando con un pulsador, sobre **tres displays multiplexados**.

### Qué hace

### 1. Conversión

```c
unsigned char valor;
valor = read_adc(0);          // 0..255

intensidad = (50 * valor) / 255;                        // Celsius
intensidad = ((9 * ((50 * valor) / 255)) / 5) + 32;      // Fahrenheit
```

El rango del sensor es de **0 a 50 °C**, mapeado a los 256 niveles del ADC.
La conversión a Fahrenheit aplica `F = (9/5)·C + 32`.

### 2. Multiplexado de tres dígitos

Sólo se puede encender un dígito a la vez, así que el bucle recorre los tres con
un selector en `PORTC`:

```c
PORTB = mem[intensidad / 100];          // primer dígito   -> PORTB
PORTC = 0x04;
PORTD = mem[(intensidad % 100 - intensidad % 10) / 10];
PORTC = 0x02;
PORTD = mem[intensidad % 10];
PORTC = 0x01;
```

### 3. Cambio de escala

```c
actual = btn;                       // btn = PINB.0
if (pasado == 1 && actual == 0)     // flanco de pulsación
    isFahrenheit = ~isFahrenheit;
if (pasado == 0 && actual == 1)
    delay_ms(1);                    // espera el rebote

pasado = actual;
```

### Las cuatro variantes

| Archivo | Referencia | Orden del selector | Retardo | Flash |
|---|---|---|---|---|
| `P16.c` | **AVcc** | `0x01 → 0x02 → 0x04` | `1 ms` | 648 B |
| `P162.c` | **AREF** | `0x04 → 0x02 → 0x01` | `100 ms` | 654 B |
| `P163.c` | **AREF** | `0x04 → 0x02 → 0x01` | `1 ms` | 654 B |
| `P164.c` | **AVcc** | `0x04 → 0x02 → 0x01` | `10 ms` | 648 B |

Los cuatro archivos se conservan porque son **cuatro pruebas de calibración
distintas**: cambiar la referencia del ADC, el orden de multiplexado y la
duración del retardo son los tres parámetros que había que ajustar hasta que los
tres dígitos se leyeran sin parpadeos.

### Observaciones

- **Conflicto de hardware en `PB0`.** El pulsador está en `PB0`, pero `DDRB` está
  configurado como **salida completa** porque `PORTB` maneja el primer dígito del
  display. Leer `PINB.0` con el pin configurado como salida devuelve el valor del
  registro de salida, no el estado del pin, así que el cambio de °C/°F no puede
  funcionar en el hardware tal como está cableado. Es el problema más relevante
  de esta práctica. Ver [NOTAS-TECNICAS.md](NOTAS-TECNICAS.md).
- `mem[]` es la tabla de 7 segmentos de 16 entradas, necesaria porque el primer
  dígito puede ser un número de dos cifras (`mem[intensidad / 100]` con
  `intensidad` de hasta 122 en Fahrenheit).
- El término **se calcula con aritmética entera** en un `unsigned char`: el
  máximo es 122 °F, que cabe sin problema en 8 bits.
- El primer dígito va en `PORTB` y los otros dos en `PORTD` porque el puerto A
  está ocupado por la entrada analógica del ADC (`PA0`).
- Es la única práctica **sin reporte**: no hay entrega formal de este ejercicio.
- Es uno de los dos proyectos compilados a **8 MHz** (junto con la práctica 14).

---

Ver también [HARDWARE.md](HARDWARE.md) · [COMPILACION.md](COMPILACION.md) ·
[NOTAS-TECNICAS.md](NOTAS-TECNICAS.md) · [GLOSARIO.md](GLOSARIO.md) ·
[README principal](../README.md)
