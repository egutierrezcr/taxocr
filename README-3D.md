# Arquitectura para el visor 360° / 3D (futuro)

Este catálogo ya deja preparada la base de datos y los puntos de montaje para una
futura versión con **modelo 3D por especie**, entrenado con múltiples imágenes de
fuentes abiertas. Nada de esto bloquea el sitio actual: hoy se sirve la **galería
multiángulo** (foto del registro PDF + fotos reales de iNaturalist).

## 1. Capa de datos (ya generada)

### `img/gallery_manifest.json`
Galería que se muestra hoy en el detalle de cada especie. 3 imágenes locales por
especie (descargadas de iNaturalist, localidad Costa Rica, licencia CC), con autor,
licencia y enlace a la observación.

```json
"10": [ { "f": "gallery/oophaga-pumilio-g1.jpg", "src": "iNaturalist",
          "by": "usuario", "lic": "CC BY-NC",
          "url": "https://www.inaturalist.org/observations/123" }, ... ]
```

### `img/training_manifest.json`  ← dataset para el modelo 3D
Catálogo de ~25 fotos por especie (URLs `large`, **no descargadas**, solo referenciadas)
con licencia y atribución. Es el conjunto de entrenamiento inicial.

```json
"10": { "sci": "Oophaga pumilio", "taxon_id": 66278, "place": "CR",
        "count": 25,
        "photos": [ { "url": ".../large.jpg", "lic": "CC BY-NC",
                      "by": "usuario", "obs": ".../observations/123" }, ... ] }
```

Para **ampliar** el dataset (recomendado 100–300 imágenes/especie para fotogrametría
o splatting), repetir la consulta a la API abierta de iNaturalist:

```
https://api.inaturalist.org/v1/observations
   ?taxon_id={taxon_id}&place_id=6924&photos=true
   &quality_grade=research&order_by=votes&per_page=200
```

`place_id=6924` = Costa Rica. Filtrar a licencias CC (cc-by, cc-by-nc, cc0, …) y
guardar la URL `large`/`original`. Complementar con otras fuentes libres
(GBIF media, Wikimedia Commons, CalPhotos).

## 2. Punto de montaje en la UI (ya colocado)

En el detalle, el panel de foto tiene una pestaña **`🧊 360° / 3D`** (`.vtab-3d`,
hoy `disabled`). Ese es el slot del visor 3D. La galería vive en la pestaña
`🖼️ Fotos`. Activar el 3D = poblar ese slot sin tocar el resto del layout.

## 3. Pipeline propuesto

1. **Descargar** el dataset por especie desde `training_manifest.json` (+ ampliación).
2. **Reconstruir** geometría por especie. Opciones:
   - Fotogrametría clásica (COLMAP / Meshroom) — requiere muchas vistas solapadas.
   - NeRF / **3D Gaussian Splatting** — mejor con fotos no calibradas y variadas.
   - Las fotos de iNaturalist NO son una secuencia de plato giratorio, así que el
     resultado será un modelo aproximado, no un escaneo métrico. Para calidad alta,
     conviene capturar/encargar tomas turntable controladas por especie.
3. **Exportar** a `.glb` (malla + textura) y alojar en `models/{slug}.glb`.
4. **Montar** con el web component `<model-viewer>` (Google) en el slot `.vtab-3d`:
   ```html
   <model-viewer src="models/oophaga-pumilio.glb"
                 camera-controls auto-rotate ar
                 style="width:100%;aspect-ratio:4/3"></model-viewer>
   ```
   Cargar el script solo cuando se abre la pestaña 3D (lazy) para no penalizar la carga.

## 4. Licencias

Las imágenes son CC (mayormente BY-NC). Uso **educativo / no comercial** con
atribución (autor + enlace a la observación, ya guardados en los manifests). Si el
sitio pasara a uso comercial, filtrar a CC-BY / CC0 únicamente antes de entrenar.
