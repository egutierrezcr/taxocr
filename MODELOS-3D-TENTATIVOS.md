# Modelos 3D — estado e identificaciones tentativas

> Kevin: las marcadas **TENTATIVA** se asignaron por color/forma (la textura es un
> atlas UV difícil de leer al 100%). En el visor aparecen con la nota
> "identificación tentativa, por confirmar". Pásame las correcciones y reasigno el `.glb`.

## ✅ Confirmadas (foto inequívoca)
| Especie | Archivo |
|---|---|
| #1 Agalychnis callidryas — Rana de Ojos Rojos | `models/agalychnis-callidryas.glb` |
| #8 Hypopachus variolosus — Rana Oveja Común | `models/hypopachus-variolosus.glb` |
| #10 Oophaga pumilio — Rana Flecha Roja y Azul | `models/oophaga-pumilio.glb` |

## ⚠️ TENTATIVAS (por confirmar)
| Asignado a | Archivo original | Archivo |
|---|---|---|
| #5 Smilisca phaeota — Rana Enmascarada | `green frog 3d model.glb (1)` | `models/smilisca-phaeota.glb` |
| #6 Smilisca sordida — Rana Arb. Parda | `frog(3)` (oliva/gris) | `models/smilisca-sordida.glb` |
| #11 Tlalocohyla loquax — Rana Arbórea Locuaz | `yellow frog 3d model` | `models/tlalocohyla-loquax.glb` |
| #14 Leptodactylus savagei — Rana Toro | `frog` / `frog(2)` (rojizo) | `models/leptodactylus-savagei.glb` |
| #7 Dendropsophus ebraccatus — Rana Arb. Amarillenta | `orange frog 3d model` | `models/dendropsophus-ebraccatus.glb` |

## 🔁 Notas
- `frog.txt` y `frog(2).txt` eran **idénticos** (mismo modelo) → se usó uno.
- `frog(1).txt` llegó **corrupto** (no era GLB) → re-descargar de Tripo.
- Todos los modelos se optimizan: 14 MB → ~2–3 MB (weld → simplify 0.2 → resize 1024).
