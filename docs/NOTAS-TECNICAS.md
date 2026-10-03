# Notas técnicas, inconsistencias y limitaciones

Este documento recoge los hallazgos de revisar los 23 archivos fuente del
repositorio. No pretende ser una crítica: es el registro honesto de lo que se
encontró, separado de lo que el coursework pedía y de lo que el código hace.

Se ordenan por gravedad.

---

## 1. Conflicto de hardware en la práctica 16 (el pulsador °C/°F)

**Gravedad: alta** · `practica-16-termometro-digital/` · los 4 archivos

El pulsador que alterna Celsius y Fahrenheit está en `PB0`:

```c
#define btn PINB.0
...
actual = btn;
if (pasado == 1 && actual == 0)   // flanco de pulsación
    isFahrenheit = ~isFahrenheit;
```

Pero `PORTB` está configurado **íntegramente como salida**, porque es el puerto
que maneja el primer dígito del display:

```c
DDRB = (1<<DDB7) | (1<<DDB6) | ... | (1<<DDB0);   // 8 salidas
...
PORTB = mem[intensidad / 100];                     // dígito 1
```

Con el pin configurado como salida, `PINB.0` devuelve el **valor del registro de
salida**, no el estado eléctrico del pin. El cambio de escala no puede
funcionar con este cableado.

**Cómo se resolvería.** Hay que mover el pulsador a un puerto que quede libre
—`PA1`–`PA7` son entradas y sólo se usa `PA0` para el ADC— y actualizar el
`#define`:

```c
#define btn PINA.1
```

Alternativamente, hay que reconfigurar `DDRB.0` dinámicamente, lo que rompería
el uso de `PORTB` como display. Mover el pulsador es la solución limpia.

---

## 2. Posible inversión izquierda/derecha en el Proyecto 2 (Bee-Bot)

**Gravedad: media** · `proyecto-02-bee-bot/P12.c`

Al **grabar**, el pulsador de izquierda escribe `1` y el de derecha escribe `2`:

```c
if (p_izq == 1 && a_izq == 0) { inst[write_index] = 1; ... }  // IZQ  -> 1
if (p_der == 1 && a_der == 0) { inst[write_index] = 2; ... }  // DER  -> 2
```

Al **reproducir**, el `switch` mapea esos mismos valores al revés:

```c
case 1: // Derecha
     PORTC = DERE;  ...
case 2: // Izquierda
     PORTC = IZQI;  ...
```

Tal como está, una trayectoria grabada giraría en sentido contrario al
grabado. Los comentarios del `switch` (`// Derecha`, `// Izquierda`) son
coherentes entre sí, pero no con el orden en que se grabó.

**Cómo se resolvería.** Intercambiar los valores de grabación:

```c
if (p_der == 1 && a_der == 0) { inst[write_index] = 1; ... }  // DER -> 1
if (p_izq == 1 && a_izq == 0) { inst[write_index] = 2; ... }  // IZQ -> 2
```

> No se modificó el código: puede que la captura se hiciera con un pulsador
> distinto del que se documentó. Conviene verificar contra la placa antes de
> «arreglarlo».

---

## 3. Frecuencia de reloj inconsistente entre prácticas

**Gravedad: media** · afecta a 7 de 23 archivos

Los proyectos quedaron compilados a dos frecuencias distintas:

| Frecuencia | Archivos |
|---|---|
| **1 MHz** | `P13.c`, `p13-2.c`, `P162.c`, `P163.c` y el resto de las prácticas |
| **8 MHz** | `P13.3.c`, `P14.c`, `P16.c` |

Como todos los temporizados son por software, un firmware compilado para 1 MHz
se ejecuta **8 veces más lento** en una placa de 8 MHz. Consecuencia práctica:
una pausa de 250 ms dura 2 s, el multiplexado del termómetro es demasiado
rápido para ver, y `read_adc()` puede quedarse esperando.

Además, las **cabeceras generadas por CodeWizard dicen 1 MHz en todos los
archivos**, incluso en los que compilaron a 8 MHz. Esas cabeceras son
información obsoleta y no reflejan la configuración real.

**Cómo se resolvería.** Fijar una sola frecuencia para todo el repositorio y
recompilar. El detalle de cada proyecto está en
[COMPILACION.md § 5](COMPILACION.md#frecuencias-de-reloj-por-proyecto).

---

## 4. El enunciado y el código no coinciden en varias prácticas

**Gravedad: media** (documental)

| Práctica | El reporte dice | El código hace |
|---|---|---|
| 07 | «Contador de 00 a 99 activado **con infrarrojo**», con material: par IR + 2N2222 | Lee un **pulsador** en `PIND.0`, con el mismo esquema antirebote de la práctica 06 |
| 05 y 06 | «Coloque un Push Button en la terminal 0 del **Puerto D**» | Usa `PINB.0` (Puerto B) en `P5.c` y `P6.c` |
| 03 | «Realizar un **contador** BCD» | Lee un valor fijo de `PORTB`; no cuenta nada internamente |
| 12 | Objetivo copiado del de la práctica 11: «hacer uso de la memoria EEPROM» | No usa EEPROM en absoluto; es el robot *Bee-Bot* |

En el caso de la práctica 07 hay que notar que **la lectura del enunciado y la
del material no coinciden entre sí**: el título dice «activado con infrarrojo» y
el paso de desarrollo dice «un Push Button en la terminal 0 del Puerto D».

> Estas divergencias se conservan tal cual. El valor de mantener el código
> original es que refleja lo que realmente se evaluó; el valor de documentarlas
> es que quien lea el repositorio no las descubra por sorpresa.

---

## 5. `read_adc()` no limpia la bandera `ADIF`

**Gravedad: media-baja** · prácticas 13, 14 y 16

Las tres prácticas usan la misma función:

```c
unsigned char read_adc(unsigned char adc_input)
{
  ADMUX = adc_input | ADC_VREF_TYPE;
  delay_us(10);
  while ((ADCSRA & (1<<ADIF)) == 0);   // espera la conversión
  return ADCH;
}
```

`ADIF` (*ADC Interrupt Flag*) se limpia escribiendo un `1` en ella. La función
espera a que se ponga en 1, pero **nunca la vuelve a limpiar**. En consecuencia,
en la segunda llamada y siguientes el `while` puede terminar de inmediato,
devolviendo un `ADCH` de la conversión anterior en lugar de una nueva.

En los proyectos 13 y 14 esto no molesta: el valor se recalcula constantemente y
cualquier valor ligeramente atrasado sirve para un display o una barra de LEDs.
En la práctica 16, donde el valor se convierte a temperatura, sí podría notarse
como lecturas truncadas.

**Cómo se resolvería.**

```c
while ((ADCSRA & (1<<ADIF)) == 0);
ADCSRA |= (1<<ADIF);        // limpiar la bandera
return ADCH;
```

---

## 6. La práctica 04 no elimina el rebote

**Gravedad: baja** (es intencionado, pero conviene decirlo)

```c
while (1){
  if (boton == 0)
  var1++;
  ...
}
```

El bucle `while(1)` se ejecuta miles de veces por segundo. Un pulsador mecánico
produce entre 5 y 20 ms de oscilaciones al presionarse, así que una sola
pulsación puede incrementar el contador varias veces.

Esto no es un descuido: es exactamente el problema que las prácticas 05 y 06
resuelven. El propio reporte de la práctica 05 lo registra como conclusión.
Se documenta porque el código de la práctica 04, aislado, parece un error.

---

## 7. Código muerto en la práctica 03

**Gravedad: baja**

```c
variable = PINB & 0x0f;      // rango 0-15
...
if (variable >= 16) {        // nunca se cumple
    PORTC = 0x79;
    PORTA = ~(0x79);
}
```

`variable` se define con una máscara de 4 bits, así que su rango máximo es 15.
La condición `variable >= 16` no puede ser cierta. Aparece dos veces: en la
rama decimal y en la hexadecimal.

Probablemente es una copia del patrón habitual de los convertidores
BCD-a-7-segmentos, donde el valor sí puede venir de una fuente más ancha.

**Cómo se resolvería.** Eliminar ambas ramas, o masking con `0xFF` si
realmente se espera un rango mayor.

---

## 8. Declaraciones huérfanas

**Gravedad: baja**

| Archivo | Declaración | Situación |
|---|---|---|
| `practica-11-memoria-eeprom/P11.c` | `void checaBoton(void);` | Declarada, nunca definida, nunca llamada |
| `proyecto-01-emisor-v1/prueba.c` | proyecto completo | `while(1)` vacío; es andamiaje sin implementar |

`checaBoton()` es un resto del desarrollo: el control de rebote del pulsador
`boton_guarda` se terminó haciendo en línea, dentro del `while`. El enlazador no
protesta porque la función nunca se llama y la optimización la elimina.

`prueba.c` se conserva porque forma parte del historial del Proyecto 1: sirvió
como esqueleto antes de escribir `P9.c` en la misma carpeta.

---

## 9. La práctica 14 tiene tres modos pero sólo dos comportamientos

**Gravedad: baja** · `practica-14-vumetro/P14.c`

La documentación de la práctica anuncia los modos **barra**, **punto** y
**hold**, pero el código sólo distingue dos casos:

```c
if (modo == 0) { /* barra: 0x01, 0x03, 0x07 */ }
else           { /* otro: 0x01, 0x03, 0x07, 0x0F */ }
```

Los valores `modo = 1` (punto) y `modo = 2` (hold) caen en la misma rama `else`.
El «hold» —congelar el valor máximo alcanzado— **no está implementado**:
dependería de hardware externo o de lógica adicional que no está en el código.

La función de suavizado también tiene una limitación de rango:

```c
num_de_unos = valor / 31;                              // 0..8
num_de_unos = (cantidad < num_de_unos) ? (num_de_unos - cantidad) : 0;
```

`cantidad` llega hasta 8, así que cuando la señal es estable (`cantidad = 8`) el
resultado es 0 para **cualquier** valor alto. La barra nunca llega a su
intensidad máxima por suavizado.

---

## 10. `proyecto-01-emisor-v2/p9.c`: configuración heredada de `PORTB`

**Gravedad: muy baja**

La inicialización generada deja `PB4` como entrada y los otros 7 bits de
`PORTB` como salida:

```c
DDRB = (1<<DDB7) | (1<<DDB6) | (1<<DDB5) | (0<<DDB4) | (1<<DDB3) | ...
```

Pero el código **no usa `PORTB` en absoluto**: los pulsadores están en `PORTD` y
la salida del motor en `PORTA`. Esta configuración es un resto de la del
**receptor**, de la que este archivo se copió al pasarlo de un microcontrolador
al otro.

Es inocuo, pero si se cablea algo en `PORTB` el comportamiento sería
contraintuitivo: `PB4` es entrada, el resto salida.

---

## 11. El multiplexado del termómetro va en orden invertido en una variante

**Gravedad: muy baja** · `practica-16-termometro-digital/P16.c`

`P16.c` recorre los selectores en el orden `0x01 → 0x02 → 0x04`, mientras que
`P162.c`, `P163.c` y `P164.c` usan `0x04 → 0x02 → 0x01`. También difieren en la
referencia del ADC (`AVcc` frente a `AREF` externa) y en el retardo entre
dígitos.

Las cuatro son notas de calibración: son cuatro configuraciones probadas
hasta que los tres dígitos se leyeran sin parpadeos. Se conservan todas por eso.

---

## 12. No existe la práctica 15

**Gravedad: documental**

La numeración salta de la práctica 14 a la 16. En el coursework hubo un tema
—cuyo número sería el 15— que no llegó a desarrollarse, o que se reservó para otro
alumno. No hay carpeta, ni código, ni reporte.

## 13. La práctica 16 no tiene reporte

**Gravedad: documental**

Es el único firmware sin entrega formal asociada. `reportes/` no contiene
ningún documento para el termómetro digital.

## 14. Dos reportes sólo existen en DOCX

**Gravedad: documental**

`reportes/Practica 12.docx` (Proyecto 2 *Bee-Bot*) y `reportes/Practica2.docx`
(puente de Wheatstone + LM741) no tienen versión PDF. Todos los demás reportes
del coursework sí la tienen.

`Practica2.docx` además **no corresponde a ningún firmware** de este repositorio:
es una práctica de instrumentación analógica —puente de Wheatstone con
calibración de `VAB(max) = 2.5 V` y amplificador **LM741** con ±7 V— sin
programa de microcontrolador asociado.

---

## Resumen

| # | Hallazgo | Gravedad | ¿Se modificó el código? |
|---|---|---|---|
| 1 | `PB0` como pulsador con `PORTB` en salida (práctica 16) | Alta | No |
| 2 | Inversión izquierda/derecha (Proyecto 2) | Media | No |
| 3 | Reloj inconsistente entre 1 y 8 MHz | Media | No |
| 4 | Enunciado y código divergen (prácticas 03, 05, 06, 07, 12) | Media | No |
| 5 | `read_adc()` no limpia `ADIF` | Media-baja | No |
| 6 | Práctica 04 sin antirebote | Baja | No |
| 7 | `variable >= 16` es código muerto (práctica 03) | Baja | No |
| 8 | `checaBoton()` sin definir; `prueba.c` vacío | Baja | No |
| 9 | La práctica 14 tiene 3 modos y 2 comportamientos | Baja | No |
| 10 | `PORTB` heredado del receptor (emisor v2) | Muy baja | No |
| 11 | Orden de multiplexado invertido en `P16.c` | Muy baja | No |
| 12 | No existe la práctica 15 | Documental | n/a |
| 13 | La práctica 16 no tiene reporte | Documental | n/a |
| 14 | Dos reportes sin PDF | Documental | n/a |

> **Decisión de principio.** Ninguno de los archivos `.c` fue modificado.
> Este repositorio es un registro de lo que se entregó en su momento, y los
> cambios de comportamiento se describen aquí para que cualquiera pueda
> aplicarlos si lo necesita. Si prefieres corregir el código, hazlo en una rama
> aparte y deja el original intacto como referencia.

---

Volver al [índice](../README.md) · Ver [PROYECTOS.md](PROYECTOS.md) ·
[HARDWARE.md](HARDWARE.md) · [COMPILACION.md](COMPILACION.md) ·
[GLOSARIO.md](GLOSARIO.md)
