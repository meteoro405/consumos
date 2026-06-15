# Conversor de Combustible — by Meteoro405

PWA para convertir consumo de combustible entre **km/l** y **l/100km**.

## Funcionalidades

- Conversión bidireccional km/l ↔ l/100km
- Doble visualización simultánea de ambos valores
- **Modo claro** (celeste/blanco) y **modo cálido** (beige/marrón, estilo De Cuestas)
- El modo seleccionado se guarda con localStorage
- Instalable como app en Android (PWA)
- Funciona offline gracias al Service Worker

## Estructura

```
consumos_v3/
├── index.html      ← app completa
├── manifest.json   ← configuración PWA
├── sw.js           ← service worker (cache: consumos-v3)
├── icons/
│   ├── icon-192.png
│   └── icon-512.png
└── README.md
```

## Despliegue en GitHub Pages

1. Crear repositorio en GitHub (ej: `conversor-combustible`)
2. Subir todos los archivos a la raíz del repositorio
3. Ir a **Settings → Pages → Branch: main → Save**
4. URL resultante: `https://meteoro405.github.io/conversor-combustible/`

## Despliegue en Netlify

Arrastrar la carpeta `consumos_v3/` al dashboard de Netlify.  
Luego Ctrl+Shift+R en el navegador para forzar recarga sin caché.

## Versionado

Al publicar una nueva versión, actualizar en `sw.js`:
```js
const CACHE = 'consumos-v4'; // incrementar número
```

## Integración con De Cuestas, Abras y Quebradas

El modo cálido (`data-theme="warm"`) replica la paleta visual de la app De Cuestas:
- `--bg: #E7D8AF` — fondo general
- `--surface: #F2E9CC` — cajas y tarjetas
- `--surface2: #e8ddb8` — botón activo y fondos secundarios
- `--text: #3b2208` — texto principal
- `--accent: #7a3b0a` — color de acento (marrón oscuro)

El ícono flotante de combustible ⛽ de De Cuestas puede apuntar a esta app como herramienta complementaria.
