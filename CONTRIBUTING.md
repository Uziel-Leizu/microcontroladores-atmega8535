# Cómo contribuir

¡Gracias por el interés! Este repositorio es un registro académico de las
prácticas de la materia *Sistemas en Chip* del IPN-ESCOM.

## Alcance

El material es **educativo**. Si eres estudiante de la misma materia y quieres
reutilizarlo, está bien: la licencia es MIT.

## Cómo aportar

1. **Fork** del repositorio y crea una rama con nombre descriptivo:

   ```bash
   git checkout -b mi-aporte
   ```

2. Trabaja en tu cambio. Respeta el estilo del código circundante:
   - indentación de 4 espacios;
   - los archivos generados por CodeVisionAVR conservan su cabecera original;
   - nada de artefactos de compilación (ya están en `.gitignore`).

3. **No** subas `.hex`, `.eep`, `.elf`, `.o`, `.map` ni carpetas `Debug/` o
   `Release/`. Para distribuir un firmware, documéntalo en el `README.md` del
   proyecto.

4. Si agregas una práctica nueva, incluye también su reporte en `reportes/`
   y una entrada en la tabla del `README.md` principal y en
   [`docs/PROYECTOS.md`](docs/PROYECTOS.md).

5. Abre un **Pull Request** describiendo qué hace el circuito y qué periféricos
   usa.

## Reglas de contenido

- Respeta la letra de la práctica: si corriges un comportamiento que
  contradecía el enunciado, déjalo anotado en la sección *Observaciones* del
  `README.md` del proyecto en lugar de cambiarlo en silencio.
- Las discrepancias entre el código y el reporte original son información
  valiosa: documéntalas en [`docs/NOTAS-TECNICAS.md`](docs/NOTAS-TECNICAS.md).

## Contacto

Para dudas sobre el contenido, abre una *issue* describiendo el proyecto y la
duda concreta.

## Código de conducta

Al participar aceptas mantener un ambiente respetuoso. Sé claro, constructivo
y presume las buenas intenciones.