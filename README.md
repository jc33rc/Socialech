# Socialech — Prototipo (Evaluar + Ejercicios + Practicar)

Prototipo interactivo de un solo archivo (`index.html`) que valida la interacción de los **3 módulos** sobre la misma base técnica: cámara + TFJS (handpose+face) + Web Speech + semáforos en vivo + reporte con Groq.

## Navegación

**Home** → 3 tarjetas:
- **Evaluar**: config (duración/tema) → sesión (5 semáforos) → reporte completo + Groq
- **Ejercicios**: catálogo (5 habilidades) → detalle (mejor marca + duración) → sesión (1 semáforo enfocado, grande y centrado) → resultado comparativo (hoy vs. mejor marca + barra de progreso)
- **Practicar**: contexto (Entrevista/Cita/Reunión/Presentación/Llamada) → check-in emocional + duración → sesión (5 semáforos) → reporte + recomendaciones de Groq adaptadas al contexto (+ mensaje de aliento si "Muy nervioso")

> Las "mejores marcas" de Ejercicios viven en memoria — se reinician al recargar la página (no hay storage persistente en este prototipo).

## Qué valida este prototipo

1. **TFJS dual-model en simultáneo**: `face-landmarks-detection` (rostro, runtime `tfjs`) + `hand-pose-detection` (manos, MediaPipeHands runtime `tfjs`), pasando el frame por un canvas intermedio.
2. **Web Speech API**: reconocimiento de voz en español (`es-ES`) para muletillas y wpm.
3. **UX en vivo**: cámara de fondo, cronómetro, calibración por encuadre, semáforos sin texto (modo completo de 5 o modo enfocado de 1 según el módulo).
4. **Reporte**: medidor circular, tarjetas con sparklines, recomendaciones de Groq — genéricas (Evaluar) o contextuales (Practicar).

## Cómo subirlo a GitHub Pages

1. Sube `index.html` (reemplaza el existente) a la raíz del repo, commit a `main`.
2. Settings → Pages ya debería seguir activo desde la config anterior — la URL se actualiza sola en ~1 min.
3. Abre **desde Chrome Android**, no desde el preview de Claude (cámara bloqueada en iframe de artifacts).

## Troubleshooting (v3)

**Si "Manos: 2" pero "Rostro: sin detección" (sin ⚠)**: se agregó un canvas intermedio antes de pasar el frame a ambos modelos — esto es un workaround conocido para quirks de input portrait en `face-landmarks-detection` runtime `tfjs`. Vuelve a probar y lee de nuevo el panel debug.

**Si sigue sin detectar rostro pero Video/FPS están bien**: aquí sí aplica tu sugerencia original — prueba a la altura de los ojos, 50-80cm, buena luz, encuadre con cabeza+torso completos. Si AÚN así "Rostro: sin detección" persiste con manos funcionando perfecto, el siguiente paso sería: (a) probar otra versión de `face-landmarks-detection` (ej. `@1.0.4` o `@1.0.2`), o (b) considerar que el detector de rostro (BlazeFace interno) tenga un requisito de input no documentado que reemplazar por una alternativa equivalente en tfjs.

**Lectura del panel debug**: igual que v2 — Rostro/Manos/Video/Inferencia/FPS/Voz/Palabras, visible automáticamente al iniciar cualquier sesión (⚙ para ocultar/mostrar).

## Próximo paso

Con los 3 módulos navegables y el canvas-fix probado: confirmar que "Rostro" pasa a "detectado" y que las métricas de mirada/postura empiezan a moverse con valores reales. Con eso, armamos el prompt verificado para Emergent.

