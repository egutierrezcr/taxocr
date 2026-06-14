# TaxoCR — Colección de Biodiversidad de Kevin Lovato

Arquitectura del catálogo. Diseñada para crecer por **ubicaciones**, **grupos
taxonómicos** y **especies**, y para navegarse por **especie, familia, ubicación,
región y rasgos** (venenosos, nocturnos, endémicos, amenazados…).

El catálogo de anfibios actual (Arbóreo · Bijagua) es **la primera hoja** del árbol.

---

## 1. Jerarquía

```
Colección (Kevin Lovato)
└── Ubicación            (Arbóreo·Bijagua, Finca Y·Caño Negro, Finca Z·Cañas…)
    └── Grupo            (Anfibios, Reptiles, Aves, Mamíferos, Insectos, Hongos…)
        └── Especie      (ficha: fotos, galería, audio, 3D, tamaño, distribución)
```

Una misma especie puede aparecer en **varias ubicaciones**. Por eso hay dos capas:

1. **Catálogos por ubicación-grupo** (lo que Kevin sube): registros + medios.
2. **Índices agregados** (derivados): índice global por especie / familia / rasgo,
   construidos a partir de todos los catálogos. Permiten las vistas transversales.

---

## 2. Estructura de carpetas

```
/data
  collection.json                 # marca, idiomas, lista de ubicaciones
  /locations
    /arboreo-bijagua
      location.json               # nombre, área, región CR, coords, grupos
      /anfibios
        catalog.json              # registros de especies de este grupo+ubicación
        /img        (foto principal del registro)
        /img/gallery (fotos multiángulo, iNaturalist CC)
        /audio       (cantos: Xeno-Canto + iNaturalist)
        /models      (.glb por especie, optimizados)
      /reptiles  …  /aves  …  (mismos subfolders)
    /finca-y-cano-negro
      location.json  …
  /_generated
    registry.json                 # auto: ubicaciones + grupos + conteos
    species-index.json            # auto: especie → dónde aparece, familia, rasgos
    family-index.json             # auto
/index.html                       # shell de la app (reusa el catálogo actual)
/build-index.mjs                  # escanea /data y regenera /_generated
```

Para **agregar** contenido, Kevin solo crea una carpeta `catalog.json` + medios y
corre (o CI corre) `build-index.mjs`. Nada más.

---

## 3. Modelo de datos de una especie (generalizado)

```json
{
  "id": "agalychnis-callidryas",
  "sci": "Agalychnis callidryas",
  "family": "Phyllomedusidae",
  "order": "Anura",
  "group": "anfibios",
  "names":   { "es": "Rana de Ojos Rojos", "en": "...", "fr": "..." },
  "traits":  ["nocturno", "arboreo"],          // tags transversales, sirven a todos los grupos
  "size":    { "min": 50, "max": 77, "unit": "mm" },
  "iucn": "LC",
  "regions": ["caribe", "zona-norte", "pacifico-sur"],
  "habitat": {...}, "diet": {...}, "description": {...}, "fact": {...},
  "media": {
    "hero":    "img/agalychnis-callidryas.jpg",        // foto del registro
    "gallery": [ {"f":"...","by":"...","lic":"...","url":"..."} ],
    "audio":   [ {"f":"...","src":"Xeno-Canto","by":"...","lic":"...","url":"..."} ],
    "model3d": { "f":"models/agalychnis-callidryas.glb", "by":"Tripo AI", "src":"..." }
  }
}
```

`traits` como lista de etiquetas (no booleanos) hace que **venenosos, nocturnos,
endémicos, amenazados, arbóreos…** funcionen igual para ranas, serpientes u hongos.

La ficha es **consciente del grupo**: el audio solo se muestra para grupos que vocalizan;
los hongos no tendrían `audio` pero sí `traits` (comestible/tóxico), etc.

---

## 4. Navegación (vistas)

- **Por ubicación** → grupo → especies (drill-down; entrada principal).
- **Por grupo** (Anfibios, Reptiles…) a través de todas las ubicaciones.
- **Por especie / familia** (índice agregado, buscable; muestra en qué ubicaciones está).
- **Por rasgo** (venenosos, nocturnos, endémicos, amenazados…) — filtro transversal.
- **Por región / mapa** (mapa de CR con pines de ubicaciones; reutiliza el mapa actual).
- Todo **trilingüe** ES/EN/FR.

Migaja: `Kevin Lovato › Arbóreo (Bijagua) › Anfibios › Rana de Ojos Rojos`.

El **catálogo actual** (grid + ficha con fotos/galería/audio/3D/tamaño/mapa) se
convierte en el **componente reutilizable** para cualquier (ubicación, grupo).

---

## 5. Pipeline de modelos 3D

Por especie: foto real → recorte IA (ISNet, fondo limpio) → Tripo **Modelo HD** → GLB.
**Optimizar siempre** (los GLB de Tripo pesan ~14 MB):

```
gltf-transform weld     in.glb a.glb
gltf-transform simplify a.glb  b.glb --ratio 0.2 --error 0.01
gltf-transform resize   b.glb  out.glb --width 1024 --height 1024
```

Sin draco/meshopt/webp (el model-viewer vendorizado no trae esos decodificadores).
Resultado ~2–3 MB, carga nativa. Dataset de entrenamiento futuro: `training_manifest.json`.

---

## 6. Contribución (cómo crece)

- **Nuevo grupo en una ubicación:** crear `locations/<loc>/<grupo>/catalog.json` + medios,
  añadir el grupo a `location.json`, correr `build-index.mjs`.
- **Nueva ubicación:** crear `locations/<loc>/location.json`, añadirla a `collection.json`,
  correr `build-index.mjs`.
- Deploy estático (GitHub Pages). Idealmente una GitHub Action corre el build al hacer push.

---

## 7. Estado

- ✅ Catálogo de anfibios Arbóreo·Bijagua (21 especies, fotos, audio, galería, mapa, tamaño).
- ✅ Visor 3D + 2 modelos (ojos rojos, oveja) optimizados.
- ⏳ Migrar el catálogo a esta estructura.
- ⏳ `build-index.mjs` + índices agregados + vistas transversales.
