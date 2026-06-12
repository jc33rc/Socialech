# Socialech — Prototipo Evaluar (validación TFJS + UX)

Prototipo interactivo de un solo archivo (`index.html`) que valida el flujo completo del módulo **Evaluar**: configuración → calibración → sesión en vivo con cámara → reporte con feedback de Groq.

## Qué valida este prototipo

1. **TFJS dual-model en simultáneo**: `handpose` (manos) + `face-landmarks-detection` (rostro, runtime `tfjs`, sin MediaPipe) corriendo juntos sobre el feed de cámara, a ~7-8 Hz.
2. **Web Speech API**: reconocimiento de voz en español (`es-ES`) para detectar muletillas y calcular ritmo (wpm).
3. **UX en vivo**: cámara de fondo, cronómetro, calibración por encuadre (óvalo guía), 5 indicadores tipo "hoja" (semáforo mint/ámbar/coral) sin texto, transiciones suaves.
4. **Reporte**: medidor circular de puntaje, tarjetas de métricas con mini-gráficos, recomendaciones generadas por Groq (llama-3.3-70b-versatile).
5. **Sistema visual**: paleta verde-menta, tipografía Outfit + Plus Jakarta Sans, animaciones de "respiración", glow en estados activos.

## Cómo subirlo a GitHub Pages

1. Crea (o usa) un repo, sube `index.html` a la raíz (o a `/docs`).
2. En **Settings → Pages**, selecciona la rama y carpeta donde está `index.html`.
3. Abre la URL que GitHub Pages genera **desde el navegador del celular** (Chrome Android recomendado, igual que KinearFit).

> ⚠️ **Importante**: si abres este HTML dentro de la vista previa de Claude.ai, la cámara probablemente NO funcionará (los iframes de artifacts no delegan permisos de cámara/micrófono). Pruébalo siempre en una pestaña real del navegador (GitHub Pages, o un servidor local).

## Cómo probar

1. Al abrir, carga los modelos TFJS (unos segundos) → pantalla de config "Evaluar".
2. Elige duración (1/2/5 min) y opcionalmente un tema.
3. Toca **Comenzar evaluación** → acepta permisos de cámara y micrófono.
4. Espera la calibración (óvalo guía) — al detectar tu rostro, arranca el cronómetro.
5. Habla con normalidad. Observa las 5 "hojas" cambiar de color en tiempo real.
6. Toca el botón **⚙ (debug)** en la esquina inferior derecha para ver FPS / tiempo de inferencia / detección de rostro y manos — esto es lo que indica si hay lag real en tu dispositivo.
7. Al terminar (o tocando el botón de stop), verás "Analizando…" y luego el reporte.
8. En el reporte, pega tu **Groq API key** en el campo indicado para generar el resumen + recomendaciones.

## Notas técnicas / pendientes

- **API key de Groq**: el campo en el reporte es solo para pruebas. En producción, esta llamada debe pasarse por el backend (Emergent) — nunca expongas la key en el cliente, especialmente si el repo es público.
- **Métricas v1 (proxies simplificados)**:
  - *Contacto visual*: orientación frontal del rostro (yaw) vs. cámara — no usa iris tracking todavía.
  - *Postura*: estabilidad del ángulo de inclinación entre ojos (roll).
  - *Gestos/manos*: solapamiento del bounding box de la mano con una zona expandida alrededor del rostro ("toque a la cara").
  - *Muletillas/ritmo*: lista de muletillas en español (`eh`, `o sea`, `bueno`, etc.) + conteo de palabras / tiempo transcurrido.
- **Mirroring**: el video se muestra en espejo (CSS), pero las coordenadas de TFJS están en el frame original (sin espejo). Esto no afecta las métricas actuales (no dependen de izquierda/derecha), pero sí importaría si en v2 se agrega coaching de "mano izquierda/derecha".
- **Multiidioma**: este prototipo solo tiene copy y reconocimiento de voz en español. La estructura (`FILLERS_ES`, strings de UI) está pensada para extenderse a EN/PT/FR/DE en la versión Emergent.
- **Si el navegador no soporta Web Speech API** (poco común en Chrome Android/iOS Safari recientes), las métricas de muletillas/ritmo simplemente no se actualizan — el resto del flujo sigue funcionando.

## Próximo paso sugerido

Con esto probado en tu dispositivo (Motorola G75 / Chrome Android), confirmamos:
- Si el FPS de detección y el tiempo de inferencia son aceptables con los dos modelos a la vez.
- Si el reconocimiento de voz en español funciona bien para muletillas/wpm.
- Si la estética/interacción se siente como se espera.

Con eso validado, armamos el prompt verificado para Emergent (auditoría técnica + integración WebView en Expo, igual que KinearFit).
