# Proyecto 1 — Emisor infrarrojo v1

> Ficha detallada, pines utilizados y observaciones en
> [`docs/PROYECTOS.md`](../docs/PROYECTOS.md).

## Identificación rápida

| Dato | Valor |
|---|---|
| Microcontrolador | ATmega8535 |
| Reloj de compilación | 1 MHz |
| Periférico principal | USART y PWM |
| Fuente principal | P9.c |
| IDE original | CodeVisionAVR 3.32 Evaluation |

## Archivos de esta carpeta

| Archivo | Descripción |
|---|---|
| `P9.c` | emisor que codifica y transmite los pulsos |
| `prueba.c` | esqueleto del proyecto, sin implementar |
| `P9.prj, prueba.prj` | proyectos CodeVisionAVR |
| `P9.atsln` | solución Atmel Studio |
| `P9.cproj` | proyecto de código Atmel Studio |

## Compilar y grabar

1. Abrir el proyecto en CodeVisionAVR 3.32:
   *Project → Open Project…* → `P9.prj`
2. Compilar: *Project → Build* (o `F7`).
3. Grabar el `.hex` generado en `Debug/Exe/`:

```bash
avrdude -p m8535 -c usbasp -U flash:w:P9.hex -V
```

> **El reloj debe coincidir con el de tu placa.** Si el firmware se compiló a
> 1 MHz y tu placa tiene un cristal de 8 MHz, todos los retardos serán 8 veces
> más cortos. Ver [`docs/COMPILACION.md`](../docs/COMPILACION.md).

La solución `.atsln` de esta carpeta también se puede abrir en **Atmel Studio 7**.

## Reporte

- [Proyecto 1.pdf](../reportes/Proyecto%201.pdf)

## Documentación del repositorio

- [`README.md`](../README.md) — índice y guía general
- [`docs/PROYECTOS.md`](../docs/PROYECTOS.md) — ficha completa de cada práctica
- [`docs/HARDWARE.md`](../docs/HARDWARE.md) — pinout del ATmega8535 y materiales
- [`docs/COMPILACION.md`](../docs/COMPILACION.md) — compilar, grabar y fuses
- [`docs/NOTAS-TECNICAS.md`](../docs/NOTAS-TECNICAS.md) — inconsistencias detectadas
- [`docs/GLOSARIO.md`](../docs/GLOSARIO.md) — términos técnicos