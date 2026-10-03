# Práctica 16 — Termómetro digital

> Ficha detallada, pines utilizados y observaciones en
> [`docs/PROYECTOS.md`](../docs/PROYECTOS.md).

## Identificación rápida

| Dato | Valor |
|---|---|
| Microcontrolador | ATmega8535 |
| Reloj de compilación | 8 MHz (P16.c) y 1 MHz (P162.c, P163.c y P164.c) |
| Periférico principal | ADC y display de 7 segmentos |
| Fuente principal | P16.c |
| IDE original | CodeVisionAVR 3.32 Evaluation |

## Archivos de esta carpeta

| Archivo | Descripción |
|---|---|
| `P16.c` | versión compilada a 8 MHz |
| `P162.c, P163.c y P164.c` | variantes a 1 MHz con distinto orden de multiplexado |
| `P16.prj, P162.prj, P163.prj, P164.prj` | proyectos CodeVisionAVR |
| `P16.atsln` | solución Atmel Studio |
| `P16.cproj` | proyecto de código Atmel Studio |

## Compilar y grabar

1. Abrir el proyecto en CodeVisionAVR 3.32:
   *Project → Open Project…* → `P16.prj`
2. Compilar: *Project → Build* (o `F7`).
3. Grabar el `.hex` generado en `Debug/Exe/`:

```bash
avrdude -p m8535 -c usbasp -U flash:w:P16.hex -V
```

> **El reloj debe coincidir con el de tu placa.** Si el firmware se compiló a
> 1 MHz y tu placa tiene un cristal de 8 MHz, todos los retardos serán 8 veces
> más cortos. Ver [`docs/COMPILACION.md`](../docs/COMPILACION.md).

La solución `.atsln` de esta carpeta también se puede abrir en **Atmel Studio 7**.

## Reporte

**No hay reporte en este repositorio.** Es el único firmware sin entrega formal asociada.

## Documentación del repositorio

- [`README.md`](../README.md) — índice y guía general
- [`docs/PROYECTOS.md`](../docs/PROYECTOS.md) — ficha completa de cada práctica
- [`docs/HARDWARE.md`](../docs/HARDWARE.md) — pinout del ATmega8535 y materiales
- [`docs/COMPILACION.md`](../docs/COMPILACION.md) — compilar, grabar y fuses
- [`docs/NOTAS-TECNICAS.md`](../docs/NOTAS-TECNICAS.md) — inconsistencias detectadas
- [`docs/GLOSARIO.md`](../docs/GLOSARIO.md) — términos técnicos