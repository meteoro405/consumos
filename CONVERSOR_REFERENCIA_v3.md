# CONVERSOR DE COMBUSTIBLE — REFERENCIA COMPLETA v3
> Documento de continuidad para Claude. Contiene todo lo necesario para retomar el proyecto sin pérdida de información.

---

## 1. DESCRIPCIÓN DEL PROYECTO

**Nombre:** Conversor de Combustible  
**Autor:** Ariel Ferro (GitHub: meteoro405)  
**Título en app:** "Conversor by Meteoro405"  
**Versión actual:** v3  
**Tipo:** PWA (Progressive Web App) — HTML/CSS/JS puro, sin frameworks  
**Deploy:** Netlify (también compatible con GitHub Pages)  
**Relación:** App satélite del ecosistema Meteoro405, complementaria a "De Cuestas, Abras y Quebradas"

---

## 2. FUNCIONALIDAD

### Conversión
- **Modo A:** km/l → l/100km  
- **Modo B:** l/100km → km/l  
- Fórmula: `resultado = 100 / valor_ingresado` (idéntica en ambas direcciones)
- Se muestran ambos valores simultáneamente en el "dual display" al ingresar un número

### Modos de tema (selector con botón 🌿/☀️ en header)
- **Tema claro** (`data-theme="light"`): fondo celeste `#f0f8ff`, cajas blancas, acento azul `#0090d4`
- **Tema cálido** (`data-theme="warm"`): fondo beige `#E7D8AF`, cajas `#F2E9CC`, acento marrón `#7a3b0a` — replica paleta de "De Cuestas, Abras y Quebradas"
- El tema se persiste en `localStorage` con la clave `consumos-theme`

### PWA
- Instalable en Android desde Chrome ("Agregar a pantalla de inicio")
- Funciona offline gracias al Service Worker
- Caché: `consumos-v3` (actualizar número en `sw.js` en cada release)

---

## 3. ESTRUCTURA DE ARCHIVOS

```
consumos_v3/
├── index.html         ← toda la app (HTML + CSS + JS en un solo archivo)
├── manifest.json      ← configuración PWA
├── sw.js              ← Service Worker
├── icons/
│   ├── icon-192.png   ← ícono PWA (fondo celeste, símbolo blanco)
│   └── icon-512.png
└── README.md          ← instrucciones de deploy
```

**ZIP de distribución:** `consumos_v3.zip` con estructura interna `consumos_v3/[archivos]`

---

## 4. CÓDIGO COMPLETO — index.html

```html
<!DOCTYPE html>
<html lang="es" data-theme="light">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <meta name="theme-color" content="#f0f8ff" />
  <meta name="description" content="Convertí consumo de combustible entre km/l y l/100km" />
  <meta name="apple-mobile-web-app-capable" content="yes" />
  <meta name="apple-mobile-web-app-status-bar-style" content="default" />
  <meta name="apple-mobile-web-app-title" content="Conversor" />
  <title>Conversor by Meteoro405</title>
  <link rel="manifest" href="manifest.json" />
  <link rel="icon" href="icons/icon-192.png" />
  <link rel="apple-touch-icon" href="icons/icon-192.png" />
  <link href="https://fonts.googleapis.com/css2?family=DM+Mono:wght@400;500&family=Syne:wght@400;700;800&display=swap" rel="stylesheet" />
  <!-- [CSS y JS completos — ver sección 4a y 4b] -->
</head>
```

### 4a. Variables CSS clave

```css
/* TEMA CLARO */
:root, [data-theme="light"] {
  --bg: #f0f8ff;
  --surface: #ffffff;
  --surface2: #e1f0fb;
  --border: rgba(0,120,200,0.1);
  --border2: rgba(0,120,200,0.2);
  --text: #0d2d4a;
  --muted: #6a9ab8;
  --accent: #0090d4;
  --accent-dim: rgba(0,144,212,0.1);
  --accent-text: #ffffff;
  --logo-icon-bg: #0090d4;
  --logo-icon-fg: #ffffff;
}

/* TEMA CÁLIDO (estilo De Cuestas) */
[data-theme="warm"] {
  --bg: #E7D8AF;
  --surface: #F2E9CC;
  --surface2: #e8ddb8;
  --border: rgba(101,67,33,0.15);
  --border2: rgba(101,67,33,0.25);
  --text: #3b2208;
  --muted: #7a5c38;
  --accent: #7a3b0a;
  --accent-dim: rgba(122,59,10,0.12);
  --accent-text: #F2E9CC;
  --logo-icon-bg: #7a3b0a;
  --logo-icon-fg: #F2E9CC;
}
```

### 4b. Lógica JS principal

```javascript
// Cambio de tema
function applyTheme(t) {
  document.documentElement.setAttribute('data-theme', t);
  themeBtn.textContent = t === 'light' ? '🌿' : '☀️';
  localStorage.setItem('consumos-theme', t);
}
themeBtn.addEventListener('click', () => {
  const current = document.documentElement.getAttribute('data-theme');
  applyTheme(current === 'light' ? 'warm' : 'light');
});

// Conversión
function convert() {
  const val = parseFloat(inputEl.value);
  if (!val || val <= 0) { /* limpiar UI */ return; }
  const res = 100 / val;
  // mostrar resultado y dual display
}

// Cambio de modo
function setMode(m) {
  mode = m; // 'kml' o 'l100'
  // actualizar labels, limpiar valores
}
```

---

## 5. CÓDIGO COMPLETO — sw.js

```javascript
const CACHE = 'consumos-v3';
const ASSETS = ['./index.html', './manifest.json', './icons/icon-192.png', './icons/icon-512.png'];

self.addEventListener('install', e => {
  e.waitUntil(caches.open(CACHE).then(c => c.addAll(ASSETS)).then(() => self.skipWaiting()));
});
self.addEventListener('activate', e => {
  e.waitUntil(
    caches.keys().then(keys => Promise.all(keys.filter(k => k !== CACHE).map(k => caches.delete(k))))
    .then(() => self.clients.claim())
  );
});
self.addEventListener('fetch', e => {
  e.respondWith(caches.match(e.request).then(cached => cached || fetch(e.request)));
});
```

---

## 6. CÓDIGO COMPLETO — manifest.json

```json
{
  "name": "Conversor de Combustible",
  "short_name": "Conversor",
  "description": "Convertí consumo de combustible entre km/l y l/100km",
  "start_url": "./index.html",
  "display": "standalone",
  "orientation": "portrait",
  "background_color": "#f0f8ff",
  "theme_color": "#f0f8ff",
  "lang": "es",
  "icons": [
    { "src": "icons/icon-192.png", "sizes": "192x192", "type": "image/png", "purpose": "any maskable" },
    { "src": "icons/icon-512.png", "sizes": "512x512", "type": "image/png", "purpose": "any maskable" }
  ],
  "categories": ["utilities"]
}
```

---

## 7. HISTORIAL DE CAMBIOS POR VERSIÓN

| Versión | Cambios |
|---------|---------|
| v1 | App inicial: conversión km/l ↔ l/100km, diseño oscuro (negro/amarillo), chips de ejemplos |
| v2 | Tema claro (celeste/blanco), eliminación de chips, título "Conversor by Ariel Ferro", badge de calidad de consumo (verde/amarillo/rojo) |
| v3 | **Versión actual.** Badge eliminado. Modo dual tema (claro + cálido estilo De Cuestas). Título "Conversor by Meteoro405". Botón selector de tema en header (🌿/☀️). Tema persiste en localStorage. README agregado. |

---

## 8. FEATURES IMPLEMENTADAS / PENDIENTES

### ✅ Implementadas
- Conversión bidireccional km/l ↔ l/100km
- Doble visualización (dual display)
- Modo claro / modo cálido con persistencia
- PWA instalable (manifest + service worker)
- Funciona offline
- Sin sugerencias de ejemplos (eliminadas en v3)
- Sin badge de calidad de consumo (eliminado en v3)

### 🔲 Pendientes / Ideas futuras
- Integración Google Analytics (Ariel tiene un G-XXXXXXXXXX pendiente de obtener; agregar snippet antes de `</head>`)
- Badge de calidad de consumo (fue implementado en v2 y revertido en v3 — puede reactivarse)
- Costo de combustible por km (precio × litros)
- Compartir resultado por WhatsApp
- Historial de consultas

---

## 9. DEPLOY

### Netlify (recomendado)
1. Descomprimir `consumos_v3.zip`
2. Arrastrar carpeta `consumos_v3/` al dashboard de Netlify
3. Ctrl+Shift+R en el navegador para forzar recarga

### GitHub Pages
1. Crear repo (ej: `conversor-combustible`)
2. Subir contenido de `consumos_v3/` a la raíz
3. Settings → Pages → Branch: main → Save
4. URL: `https://meteoro405.github.io/conversor-combustible/`

### Problema frecuente: app muestra versión anterior
- Causa: caché del navegador o service worker
- Solución rápida: Ctrl+Shift+R (computadora) o desinstalar+reinstalar (Android)
- Solución permanente: incrementar número en `const CACHE = 'consumos-vX'` del sw.js en cada release

---

## 10. RELACIÓN CON "DE CUESTAS, ABRAS Y QUEBRADAS"

La app De Cuestas tiene un botón flotante ⛽ (combustible) que puede linkear a este Conversor. El modo cálido fue diseñado específicamente para que el usuario que viene de De Cuestas vea una interfaz visualmente coherente:

- De Cuestas usa fondo `#E7D8AF` → mismo `--bg` en tema cálido
- De Cuestas usa cajas `#F2E9CC` → mismo `--surface` en tema cálido  
- Tipografías distintas pero estética afín (marrón, beige, tonos tierra)

Para integrar: el ícono ⛽ flotante de De Cuestas puede abrir esta PWA en una nueva pestaña, o puede integrarse como iframe o enlace directo a la URL de Netlify/GitHub Pages.

---

## 11. CONTEXTO DEL ECOSISTEMA METEORO405

Ariel Ferro (GitHub: meteoro405) desarrolla PWAs de temática argentina:

- **De Cuestas, Abras y Quebradas** (v100, 135 rutas): guía de rutas escénicas argentinas. CSV latin-1 con 58 columnas, generador Python, deploy en GitHub Pages.
- **¿A cuánto queda?** (v8): distancias entre 751 ciudades argentinas.
- **CAVOK** (v33+): meteorología aeronáutica con CheckWX API.
- **MiGarage** (v9): tracker de mantenimiento vehicular.
- **Conversor de Combustible** (v3): esta app.

Todas son PWAs puras (HTML/CSS/JS, sin frameworks), versionadas, con ZIPs de distribución y documentos de referencia `.md` para continuidad entre sesiones de Claude.
