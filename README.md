# Presentaciones — Desarrollo Móvil Integral

Diapositivas de clase de la materia **Desarrollo Móvil Integral** (UTEZ),
escritas en [Marp](https://marp.app/) y publicadas automáticamente en
GitHub Pages.

## Cómo funciona

1. Cada sesión es un archivo Markdown en `semana-NN/<tema>.md`, con el
   formato Marp (front-matter `marp: true` + diapositivas separadas por `---`).
2. Al hacer push a `main`, el workflow
   [`.github/workflows/pages.yml`](.github/workflows/pages.yml) compila ese
   Markdown a HTML estático con `@marp-team/marp-cli` (sin necesidad de
   Chromium, porque solo se exporta HTML) y lo despliega a GitHub Pages.
3. Cada semana queda disponible en su propia ruta:
   `https://dmi-2026.github.io/presentaciones/semana-NN/`.

## Agregar una semana nueva

Crear `semana-NN/<tema>.md` con el mismo formato Marp y hacer push a `main`.
El workflow ya compila cualquier archivo `.md` que exista bajo `semana-*/` —
no hace falta tocar el workflow para cada semana nueva.

## Ejemplo publicado

- Semana 2 — Patrones de diseño: <https://dmi-2026.github.io/presentaciones/semana-02/>
