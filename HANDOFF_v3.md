# Cíclica by Jarana Natural — Handoff Document v3

## Estado actual
- **Versión estable:** `ciclica_clean.html` (renombrar a `index.html` al subir)
- **Repositorio:** github.com/jarananatural/ciclica
- **URL pública:** jarananatural.github.io/ciclica
- **Firebase proyecto:** ciclica-jarana
- **Arquitectura:** Single HTML file, CSS + JS inline, localStorage + Firebase Firestore, PWA

---

## Stack técnico
- HTML/CSS/JS en un solo archivo (~1.6MB con avatares embebidos en base64)
- Sin frameworks
- Firebase Auth (Google login) + Firestore (sync datos)
- PWA con manifest.json + service worker (sw.js)
- Canvas API para el diagrama circular

---

## Paleta de colores Jarana
```
--salmon:     #FFB7A1   (Menstrual)
--amber:      #F0BC66   (Folicular)
--teal:       #5F9595   (Ovulatoria)
--teal-deep:  #3D7070
--slate:      #AAB8BB   (Lútea)
--cream:      #FAF7F2
--cream-dark: #EDE8E0
```
Tipografía: **Corben Bold** (títulos) + **Nunito** (cuerpo)

---

## Funcionalidades implementadas

### Onboarding
- Paso 0: nombre, fecha ciclo, duración, días sangrado, regularidad
- Paso 1: ¿Qué querés observar? (toggles, sin límite)
- Paso 2: gestión menstrual (elige avatar)
- Botón ← Atrás en todos los pasos
- Validaciones: fecha no futura, ciclo 15-60 días, sangrado 1-15 días
- Aviso cuando eligen ciclo irregular/no sé

### Diagrama circular
- Canvas con segmentos por fase, día actual destacado
- 4 paletas de color elegibles desde Ajustes: Suave, Intenso (default), Sólido vibrante, Gradiente
- Botón 🌙 Lunas — emojis lunares en cada segmento (hemisferio sur)
- Botón 💌 — tarjeta para compartir ciclo
- Botón ? — abre info del diagrama
- Botón + Nuevo ciclo — modal con 🩸
- Banner al editar días pasados o ya registrados

### Registro diario (8 secciones colapsables)
1. 🌊 Ciclo menstrual — flujo (6 niveles), color (sin marrón)
2. 🫧 Síntomas físicos — 18 chips
3. ⚡ Energía y sueño — escala + horas + calidad
4. 😊 Emociones — 21 chips (lenguaje neutro)
5. 💜 Deseo y sexualidad — deseo, relaciones, anticonceptivos (2 categorías), con quién, cómo se sintió
6. 🤸 Mi entrenamiento
7. 🌱 Fertilidad
8. 🥗 Alimentación
- Palabras clave + Diario personal
- Secciones inactivas en gris

### Panel Tu cuerpo hoy 🌿
- Por fase: resumen, hormonas (barras), ciclo ovárico, ciclo uterino
- Glosario con tooltips (12 términos)
- Botón "📊 Ver el gráfico completo" → infografía Jarana con marcador del día actual

### Calendario
- Vista mensual con fases del ciclo
- Próximas fechas estimadas con badge de precisión
- Fases lunares del mes (emojis, orientación hemisferio sur)

### Alarmas
- Copa menstrual, Próxima menstruación, Ventana fértil, Ovulación
- 💊 Tomar la pastilla (hora + días de semana)
- 💧 Tomar agua (cada X horas, entre horarios)
- 🌱 Completar mi diagrama (recordatorio de registro)
- 🩺 Mis turnos médicos
- Banner estado notificaciones

### Patrones
- Resumen del ciclo, emociones frecuentes, síntomas, energía por fase
- Palabras clave (nube + top 5)
- Rueda de patrones SVG
- Duración de fases (datos reales si hay registros, estimaciones si no)
- Predicción inteligente con badge de precisión
- Aviso para ciclos irregulares

### Mi perfil
- Datos personales, Mi ciclo, Ficha de salud (condiciones ampliadas), Gestión menstrual + hormonal
- Campo "Desde" en Gestión: solo aparece si hay tratamiento cargado
- Avatares: Fashionistas 🕶️ (6 animales) + Looks ✨ (12 ilustraciones teal sobre crema)

### Ajustes
- ¿Qué querés observar? (toggles por sección)
- Paleta del diagrama (4 opciones)
- Estilo de navegación (4 opciones, pill sólido por default)
- 🗺️ Ver recorrido de la app (tour de 8 pasos)
- ☕ Apoyar el proyecto (botón Cafecito)
- Cuenta (nombre, email, cerrar sesión)

### Tour guiado (8 pasos)
1. Diagrama circular
2. Las pestañas
3. Tu cuerpo hoy
4. Botones del diagrama
5. Alarmas (cambia pestaña automáticamente)
6. Mis patrones (cambia pestaña automáticamente)
7. Navegación principal
8. ¡Empezar!
- Overlay con hole transparente y línea SVG punteada teal
- Botones: ← Atrás, Siguiente, Saltar
- Accesible desde Ajustes

### Firebase
- Auth: Google login (popup en Android, redirect en iOS Safari)
- Firestore: sync automático al guardar
- Reglas: cada usuaria solo lee sus propios datos
- API key restringida al dominio jarananatural.github.io

### PWA
- manifest.json con start_url: "/ciclica/"
- Meta tags apple-mobile-web-app
- Instalable desde Safari (iOS) y Chrome (Android)

### Cafecito (monetización)
- Botón ☕ en Ajustes → sección "Apoyar el proyecto"
- Modal post-guardado: aparece 2 segundos después de guardar un registro diario
- Frecuencia: máximo una vez cada 60 días (key localStorage: `jn_cafecito_last`)
- URL: https://cafecito.app/jarananatural
- Para testear: `localStorage.removeItem('jn_cafecito_last')` en consola

---

## Bugs corregidos en v3 (sesión actual)

### 1. Re-login abría el onboarding
**Problema:** `loadFromFirestore()` cargaba los datos pero nunca llamaba a `launchApp()`, por lo que la app quedaba en la pantalla de onboarding aunque la usuaria ya tuviera datos.
**Fix:** Al final de `loadFromFirestore()`, si `cycle1Date` y `cycleLen` existen, llama a `launchApp(true)` (sin confetti).

### 2. Aviso médico nunca aparecía
**Problema:** `launchApp()` buscaba `disclaimerModal` pero el ID real en el HTML es `welcomeModal`.
**Fix:** Corregido el ID en `launchApp()` y en `acceptDisclaimer()`.

### 3. Campo "Desde" aparecía siempre en Perfil
**Problema:** El campo "Desde" (tiempo de tratamiento hormonal) se mostraba aunque no hubiera tratamiento cargado, mostrando "--" de forma confusa.
**Fix:** El campo solo aparece si `profile.tratamiento` tiene valor real y `profile.tiempoTratamiento` existe.

### 4. Cafecito no estaba implementado
**Fix:** Implementado desde cero (ver sección Cafecito arriba).

---

## Para testear después de subir
```javascript
// Resetear aviso médico (debe aparecer al abrir la app)
localStorage.removeItem('jn_disclaimer_v4')

// Resetear cafecito (aparece 2s después de guardar un registro)
localStorage.removeItem('jn_cafecito_last')

// Resetear todo el login (para probar flujo desde cero)
localStorage.removeItem('jn_loggedin')
localStorage.removeItem('jn_skip_login')
```

---

## Bugs conocidos / pendientes

### PENDIENTE IMPORTANTE
- Cuando se cambie el dominio (Namecheap), agregar mensaje en la app explicando que los datos están seguros en Firebase y accesibles en la versión definitiva
- Tour paso 1/8 puede desencajarse si el canvas no terminó de renderizar (timing conocido)

### Decisiones técnicas críticas
- **`loadFromFirestore()`** llama a `launchApp(true)` si el onboarding ya fue completado
- **`launchApp(skipConfetti)`** acepta parámetro para no tirar confetti en re-login
- **`obNextStatic()`** setea `obStep` a 1; `renderObStep()` usa `OB_STEPS[obStep-1]`
- **`parseDateInput()`** evita desfasaje UTC
- **`getCycleAvg()`** calcula promedio real de ciclos históricos
- Antes de modificar verificar que estas funciones aparezcan exactamente una vez:
  `const OB_STEPS`, `function renderProfile`, `function launchApp`, `function obNextStatic`, `function handleTagInput`, `function saveDay`

---

## IDEAS A FUTURO (backlog)
1. Foto en registro diario (ej: foto de manchado)
2. Foto de perfil en lugar de avatar
3. Exportar datos PDF/CSV
4. Modo oscuro
5. Widget pantalla inicio
6. Modo acompañante/pareja
7. Comparar ciclos en gráfico
8. Notas de voz en registro
9. Insights personalizados
10. Wearables temperatura basal
11. Modo embarazo/postparto
12. Contenido educativo Jarana expandido
13. Comunidad anónima
14. País/zona horaria e idioma
15. Pro bundle con productos físicos Jarana
16. Pro versión (pago único)
17. Módulo educativo premium
18. Código de acceso durante testing (controlar quién descarga)

---

## Archivos del proyecto
- `ciclica_clean.html` → renombrar a `index.html` antes de subir a GitHub
- `manifest.json` → PWA config (start_url: "/ciclica/")
- `sw.js` → service worker para notificaciones
- `icon-192.png` / `icon-512.png` → íconos PWA (logo Jarana + "Cíclica by")

---

## Contexto de negocio
- **Jarana Natural:** marca argentina de productos menstruales sostenibles
- **Público objetivo:** personas con ciclo menstrual (lenguaje neutro/inclusivo)
- **Idioma:** Rioplatense español (tuteo)
- **Tagline:** "celebremos lo natural."
- **4 fases:** Momento Reflexivo (Menstrual), Activo (Folicular), Expresivo (Ovulatoria), Creativo (Lútea)
- **Contenido educativo:** basado en libro "Hoy comienza un nuevo ciclo" de Jarana Natural
- **Testing:** fase de testing con clientas en curso
- **Dominio:** en proceso de configurar dominio personalizado en Namecheap
- **Monetización:** Cafecito (gratuita para siempre), futuro Pro con MercadoPago

---

## Instrucciones para continuar en nuevo chat
Al iniciar una nueva sesión, decile a Claude:
> "Estoy trabajando en Cíclica by Jarana Natural, una app PWA de seguimiento de ciclo menstrual. El archivo de trabajo se llama index.html y está en /mnt/project/. Acá está el handoff con el estado actual: [pegar este documento]"
