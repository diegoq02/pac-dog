---
name: pixel-art
description: Convención de sprites 8-bit del proyecto PAC-DOG — cómo definir, colorear y renderizar pixel-art por código en canvas, para mantener consistencia entre los cuatro personajes.
---

# Convención de pixel-art (PAC-DOG)

Todo el arte del proyecto se genera por código en `<canvas>`: **nada de imágenes rasterizadas** (PNG/JPG/base64).

## Formato de sprite

Un sprite es un **arreglo de strings** donde cada string es una fila y cada carácter un píxel:

- **Grid**: 16×16 para personajes (perritas y fantasmas), 8×8 para ítems (hueso, croqueta, corazón).
- **Regla estricta**: todas las filas de un sprite miden exactamente lo mismo (16 o 8 chars). Verificar largos antes de integrar.
- `.` = píxel transparente. Cualquier otro carácter es un índice en la paleta del sprite.

```js
const NEGRITA_ABIERTA = [
  "................",
  ".........k..k...",
  // ... 16 filas de 16 chars
];
```

## Paleta

Cada personaje tiene **su propia paleta**: objeto `{ carácter: colorCSS }`. El carácter `.` no se incluye (transparente). Convención de letras: minúsculas para el cuerpo (`k` negro, `b` marrón, `s` montura...), y letras fijas entre personajes para rasgos comunes: `w` = blanco de ojo, `e` = pupila, `g`/`m` = hocico, `L` = montura de lentes.

```js
const PAL_NEGRITA = { k:"#2e2e36", g:"#a9a9b0", w:"#ffffff", e:"#141414" };
```

## Renderizado

Usar el helper único `makeSprite(filas, paleta, flipX)`:

1. Crea un canvas offscreen del tamaño del grid.
2. Pinta cada píxel con `fillRect(x, y, 1, 1)` (o `gridW-1-x` si `flipX`).
3. Devuelve el canvas; se dibuja escalado con `drawImage` y **siempre** con `ctx.imageSmoothingEnabled = false` + CSS `image-rendering: pixelated`.

Los sprites se **pre-renderizan una sola vez al cargar** (incluidas las variantes espejadas y los frames de animación); nunca pintar píxel a píxel dentro del game loop.

## Animación y variantes

- Perritas: 2 frames (boca abierta / cerrada) mirando a la derecha; la izquierda es `flipX`. Arriba/abajo reutilizan el último flip horizontal.
- Fantasmas: sprite base + variante **asustada** (silueta azul `#2121de`, ojos y boca ondulada blancos) + variante **flash** (cuerpo blanco, rasgos rojos) para avisar que el power-up termina.
- Los rasgos identitarios van primero: si hay que sacrificar detalle, se conservan orejas/lentes/pelo antes que sombras.

## Personajes canon

| Personaje | Rasgos irrenunciables |
|---|---|
| Negrita | pelaje negro, orejas triangulares erguidas, hocico gris canoso |
| Orejas | pelaje tostado, montura negra en lomo, cejitas tostadas, una oreja erguida y una caída |
| Nico | cuerpo fantasma blanco, piel morena, lentes redondos, cabello castaño oscuro largo a los lados |
| Diego | cuerpo fantasma blanco + franja de collar hawaiano neón, piel clara, pelo negro rizado voluminoso (borde superior irregular), lentes, sonrisa con braces |
