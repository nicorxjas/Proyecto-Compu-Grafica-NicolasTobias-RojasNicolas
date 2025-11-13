# Proyecto-Compu-Grafica-NicolasTobias-RojasNicolas

**Descripción:**
- **Proyecto:** Visualización 3D interactiva usando Three.js con shaders personalizados.
- **Autor:** Nicolás Tobias Rojas (repositorio: nicorxjas/Proyecto-Compu-Grafica-NicolasTobias-RojasNicolas)
- **Propósito:** Demo/pequeño proyecto de computación gráfica para la asignatura de Compu Gráfica. Muestra un torus con un material escrito en GLSL (vertex/fragment shaders), postprocesado (bloom, FXAA/SMAA) y controles interactivos.

**Características principales:**
- **Three.js** como motor 3D.
- **Shaders personalizados** en `src/shaders/vertex.js` y `src/shaders/fragment.js`.
- **Postprocesado** con `EffectComposer`, `UnrealBloomPass` y pases de render.
- **Interfaz de depuración** (GUI) usando `lil-gui` (incluida localmente en `src/init/lil-gui.module.min.js`).
- **Controles de cámara** usando `SmoothOrbitControls.js` en `src/init`.

**Requisitos previos:**
- Node.js (versión 16+ recomendada).
- npm (v8+ o la versión que venga con Node.js instalada).

**Instalación (rápida):**
1. Clona el repositorio o descarga el código en tu máquina.
2. Abre una terminal en la raíz del proyecto (donde está `package.json`).
3. Ejecuta:

```powershell
npm install
```

Esto instalará las dependencias listadas en `package.json`.

**Modo desarrollo (levantar servidor):**
- Para iniciar el servidor de desarrollo y ver la escena en tu navegador ejecuta:

```powershell
npm run start
```

- El servidor de desarrollo está configurado con webpack (archivos bajo `bundler/webpack.dev.js`). Normalmente abrirá una pestaña en tu navegador o mostrará la URL (por ejemplo `http://localhost:8080`).

**Generar build de producción:**

```powershell
npm run build
```

El bundle de producción se generará según la configuración en `bundler/webpack.prod.js`.

**Estructura del proyecto (resumen):**
- `src/` : código fuente.
	- `app.js` : entry point principal que monta la escena, añade el torus y el pipeline de postprocesado.
	- `index.html` : plantilla HTML cargada por webpack dev server.
	- `style.css` : estilos básicos (canvas, cuerpo de la página).
	- `init/` : utilidades de inicialización.
		- `init.js` : inicializa el renderer, el canvas, la escena, la cámara y exporta `init`, `gl`, `scene`, `camera`, `controls`.
		- `lil-gui.module.min.js` : versión local de `lil-gui` (se usa en lugar de `dat.gui`).
		- `SmoothOrbitControls.js` : controles de cámara personalizados.
	- `shaders/` : shaders GLSL empaquetados como módulos JS.
		- `vertex.js` : shader de vértices.
		- `fragment.js` : shader de fragmentos.
- `bundler/` : configuración de webpack para dev/prod.
- `static/` : recursos estáticos (si aplica).

**Notas importantes / solución de errores comunes:**
- Error: "Can't resolve 'dat.gui'": Este repositorio incluye `lil-gui` local en `src/init`. En vez de instalar `dat.gui`, asegúrate de que `src/init/init.js` importe `lil-gui` localmente (por ejemplo `import { GUI } from './lil-gui.module.min.js'`). Alternativamente instala `dat.gui` con `npm install dat.gui --save`.
- Si `node_modules` está incompleto o encuentras errores de dependencias, borra `node_modules` y `package-lock.json` y vuelve a ejecutar `npm install`:

```powershell
rd /s /q node_modules; del package-lock.json; npm install
```

**Cómo funciona el código a alto nivel:**
- `src/app.js` realiza las siguientes tareas principales:
	- Llama a `init()` (en `src/init/init.js`) para crear el `renderer`, `canvas`, `scene`, `camera` y `controls`.
	- Crea un `Mesh` tipo `TorusGeometry` y aplica un `ShaderMaterial` con los shaders importados desde `src/shaders`.
	- Añade un `EffectComposer` para aplicar pases de postprocesado (`RenderPass`, `UnrealBloomPass`, etc.).
	- Usa `lil-gui` para exponer uniformes del shader (desplazamiento, spread, ruido).
	- Ejecuta el bucle de animación con `requestAnimationFrame`, actualiza uniformes (`uTime`) y renderiza a través del `composer`.

**Shaders y uniforms:**
- En `app.js` el material del torus define estos `uniforms` principales:
	- `uTime` : tiempo transcurrido — suele animar el efecto.
	- `uResolution` : resolución del canvas (Vector2).
	- `uDisplace` : magnitud de desplazamiento (controlada por GUI).
	- `uSpread` : parámetro para controlar la extensión del efecto.
	- `uNoise` : intensidad/frecuencia del ruido.

Modifica esos valores para ver cambios visuales. Si editas los archivos de shader, webpack recargará la página en modo dev (hot reload) si está configurado.

**Depuración y desarrollo:**
- Abre la consola del navegador (F12) para ver errores GLSL o errores de ejecución.
- Si el canvas aparece negro, verifica que la cámara esté apuntando al `mesh` y que `renderer` tenga el tamaño correcto (`renderer.setSize(window.innerWidth, window.innerHeight)` debería ejecutarse en `init`).