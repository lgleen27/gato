# Instrucciones para construir la prueba del Gato con Antigravity

**Complementa a:** `plan-prueba-app-gato-ionic-laravel.md` (el plan técnico por etapas)
**Fecha:** 04/11/2026
**Estado:** Borrador para revisión.

> Antigravity es un IDE de Google orientado a agentes: tú describes tareas y un agente planifica, escribe código, ejecuta comandos y verifica resultados. Los nombres exactos de menús y opciones pueden cambiar entre versiones; si algo no coincide con lo descrito aquí, confía en lo que veas en la interfaz.

---

## 1. Cómo usar este documento

1. Haz la **preparación manual** (sección 3).
2. Crea el **workspace** con la estructura de la sección 4.
3. Configura Antigravity como se sugiere en la sección 5.
4. Copia el **AGENTS.md** (sección 6) a la raíz del workspace.
5. Pega el **prompt inicial** (sección 7) en una conversación nueva.
6. Avanza **una etapa a la vez** con los prompts de la sección 8, revisando cada resultado antes de continuar.

---

## 2. Reparto de tareas: qué hace el agente y qué haces tú

| Tarea | Agente | Tú |
|---|---|---|
| Instalar Antigravity, Node, PHP, Composer, Git | | ✅ |
| Instalar Android Studio, SDK 36 y crear el emulador | | ✅ |
| Verificar versiones instaladas (solo lectura) | ✅ | Revisas el reporte |
| Crear proyectos Ionic Vue y Laravel | ✅ | Apruebas comandos |
| Escribir el código del juego, la API y el cliente | ✅ | Revisas y pruebas |
| `ionic build`, `npx cap add/sync android` | ✅ | |
| Abrir Android Studio, sincronizar Gradle, **ejecutar en emulador o teléfono** | | ✅ |
| Probar la app en el dispositivo y decir qué pasó | | ✅ |
| Diagnosticar errores a partir de lo que tú reportes | ✅ | Pasas el error completo |
| Decidir si se continúa a la siguiente etapa | | ✅ |

> **Importante:** Antigravity **no reemplaza a Android Studio**. El SDK de Android, Gradle y el emulador siguen viniendo de Android Studio; el agente solo escribe el código y ejecuta comandos desde la terminal.

---

## 3. Preparación manual (antes de abrir el agente)

### 3.1 Instalar

| Herramienta | Requisito |
|---|---|
| Antigravity | Última versión disponible |
| Node.js | 22 o superior |
| PHP | 8.3 o superior |
| Composer | 2.x |
| Git | Reciente |
| Android Studio | "Otter" 2025.2.1 o superior |
| Android SDK | Plataforma API 36, Build-Tools y Platform-Tools |
| Emulador o teléfono | Emulador con API 33+ o teléfono con Depuración USB |

### 3.2 Verificar en tu terminal

```bash
node --version     # v22.x o superior
npm --version
php --version      # 8.3 o superior
composer --version
git --version
```

### 3.3 Variable de entorno de Android (si Android Studio no la configuró)

```
ANDROID_HOME = <ruta del Android SDK>
PATH += <ANDROID_HOME>/platform-tools
```

---

## 4. Estructura del workspace

Abre en Antigravity **una carpeta padre** que contenga ambos proyectos, para que el agente vea frontend y backend a la vez:

```
gato-proyecto/                     <- esta carpeta es el workspace
├── AGENTS.md                      <- reglas del agente (sección 6)
├── docs/
│   ├── plan-prueba-app-gato-ionic-laravel.md
│   └── instrucciones-antigravity-gato.md
├── gato-app/                      <- lo crea el agente (Etapa 1)
└── gato-api/                      <- lo crea el agente (Etapa 3)
```

Pasos:

```bash
mkdir gato-proyecto
cd gato-proyecto
mkdir docs
# copia aquí los dos archivos .md dentro de docs/
# crea AGENTS.md en la raíz con el contenido de la sección 6
git init
```

Luego, en Antigravity: **abrir carpeta** → `gato-proyecto`.

---

## 5. Configuración recomendada en Antigravity

Estas opciones aparecen con nombres similares en la configuración y al iniciar una conversación:

| Ajuste | Recomendación para esta prueba | Motivo |
|---|---|---|
| **Modo de planificación** | *Planning* para las etapas, *Fast* solo para arreglos pequeños | Obtienes un plan que puedes revisar antes de que el agente escriba código |
| **Política de revisión** | Que el agente **pida revisión** (o que decida él, "Agent Decides") | Quieres validar cada paso, no darle control total |
| **Ejecución de comandos en terminal** | Modo **Off** o **Auto**, nunca el modo totalmente automático (*Turbo*) | Evita que instale o borre cosas sin que lo veas |
| **Lista de comandos denegados** | Agregar comandos destructivos (por ejemplo `rm -rf`, `sudo`) | Capa extra de seguridad |
| **Modelo** | El mejor disponible en tu cuenta para tareas de código | |

Dos vistas útiles:
- **Agent Manager:** panel para iniciar conversaciones con agentes y seguir su progreso.
- **Editor:** vista tipo editor de código para revisar y modificar archivos a mano.

**Reglas y flujos de trabajo (opcional, más adelante):**
- Instrucciones permanentes para todos los agentes del proyecto: archivo `AGENTS.md` en la raíz.
- Reglas propias de Antigravity: carpeta `.agent/rules/` del workspace.
- Prompts reutilizables que se invocan con `/`: carpeta `.agent/workflows/`.
- Regla global para todas tus carpetas: `~/.gemini/GEMINI.md`.

Para esta prueba basta con `AGENTS.md`.

---

## 6. Contenido de `AGENTS.md` (copiar a la raíz del workspace)

````markdown
# AGENTS.md: Prueba de concepto "Gato"

## Contexto
Prueba de concepto desechable para validar el stack Vue + Ionic + Capacitor + Laravel
en una app de Android. El proyecto es un juego de gato (tic tac toe) que guarda el
historial de partidas en una API de Laravel.

La fuente de verdad técnica es `docs/plan-prueba-app-gato-ionic-laravel.md`.
Léelo completo antes de actuar y consúltalo en cada etapa.

## Reglas de trabajo
1. Trabaja UNA etapa a la vez, en el orden del plan. Al terminar una etapa, detente,
   presenta el reporte (ver formato abajo) y espera mi confirmación antes de continuar.
2. Antes de ejecutar cualquier etapa, presenta un plan breve y espera mi aprobación.
3. No instales nada global (`npm -g`, `composer global`, etc.) ni fuera de este
   workspace sin pedirme confirmación explícita, indicando qué y por qué.
4. No inventes versiones ni comandos. Si dudas de un requisito o de un comando,
   verifícalo en la documentación oficial y cita la fuente.
5. Si algo del plan no funciona o conviene cambiarlo, proponlo y explica el motivo;
   no lo cambies en silencio.
6. No modifiques configuraciones de Android Studio ni del sistema operativo.
   Las tareas que dependen de Android Studio (sincronizar Gradle, ejecutar en
   emulador o teléfono) las hago yo; indícame los pasos exactos.
7. Nunca imprimas, copies ni subas secretos (archivos `.env`, tokens, claves).
8. Haz un commit de Git al cerrar cada etapa, con mensaje en español
   (por ejemplo: "Etapa 1: juego de gato en Ionic Vue").
9. Si un comando falla, muestra el error completo y diagnostica antes de reintentar;
   no repitas el mismo comando esperando otro resultado.

## Stack y convenciones
- Frontend (`gato-app/`): Vue 3, TypeScript, Ionic Vue, Capacitor 8, `<script setup>`.
- Backend (`gato-api/`): Laravel 13, PHP 8.3 o superior, SQLite.
- Sanctum se instala con `install:api` pero solo se usa en la Etapa 5 (opcional).
- Nombres de dominio en español (Partida, tablero, ganador); respuestas y comentarios en español.
- Plataforma objetivo: solo Android. iOS queda fuera de esta prueba.

## Fuera de alcance
Publicación en Google Play, diseño visual elaborado, modo offline, juego en línea
entre dos teléfonos, iOS.

## Formato del reporte al terminar una etapa
1. Qué se hizo (lista breve).
2. Archivos creados o modificados.
3. Comandos ejecutados y su resultado.
4. Criterios de éxito del plan: marcar cuáles se cumplen y cuáles no.
5. Qué debo hacer yo manualmente para verificar (si aplica).
6. Problemas, dudas o decisiones pendientes.
````

---

## 7. Prompt inicial (Etapa 0: reconocimiento, sin instalar ni crear nada)

Pégalo en una conversación nueva, con el modo *Planning* activado:

````text
Vamos a construir paso a paso una prueba de concepto: una app Android de juego de
gato (tic tac toe) con Vue + Ionic + Capacitor, que guarda el historial de partidas
en una API de Laravel. Es un proyecto desechable para validar si este stack es
viable para un proyecto real.

Antes de hacer cualquier cosa:
1. Lee completos AGENTS.md y docs/plan-prueba-app-gato-ionic-laravel.md.
   Son la fuente de verdad y tus reglas de trabajo.
2. Este es un paso de SOLO LECTURA. No instales nada, no crees proyectos
   y no modifiques archivos.

Tu tarea (Etapa 0):
a) Detecta mi sistema operativo y verifica las versiones instaladas de Node, npm,
   PHP, Composer y Git. Compáralas con los requisitos de la sección 4 del plan.
b) Dime si existe ANDROID_HOME configurado y si adb está disponible en el PATH.
c) Comprueba si ya existen las carpetas gato-app y gato-api en este workspace.
d) Presenta una tabla con: herramienta | versión encontrada | requisito | estado (OK / falta / versión baja).
e) Resume en 5 líneas cómo entendiste el plan y el orden de las etapas.
f) Señala cualquier ambigüedad, riesgo o requisito del plan que quieras confirmar
   conmigo, verificándolo en la documentación oficial si hace falta.
g) Detente y espera mi confirmación. No avances a la Etapa 1 por tu cuenta.
````

---

## 8. Flujo por etapas y prompts de continuación

Regla de oro: **no pases a la siguiente etapa hasta cumplir los criterios de éxito de la actual**. Haz el commit antes de continuar.

### Etapa 1: Frontend del juego (solo navegador)

````text
Confirmo la Etapa 0. Procede con la Etapa 1 del plan (sección 6).
Primero muéstrame tu plan de ejecución y espera mi aprobación.
Crea el proyecto gato-app con Ionic Vue + Capacitor, implementa el juego en
src/views/HomePage.vue y verifica que arranca con `ionic serve`.
Al terminar, entrégame el reporte con el formato de AGENTS.md y los criterios de éxito de la sección 6.4.
````

**Tú:** abre la URL que indique el agente (normalmente `http://localhost:8100`), juega unas partidas y confirma que funciona.

### Etapa 2: Capacitor y Android

````text
La Etapa 1 está validada. Procede con la Etapa 2 (sección 7 del plan).
Ejecuta ionic build, npx cap add android y npx cap sync android.
NO abras Android Studio ni intentes ejecutar en un emulador: eso lo hago yo.
Al terminar, dame los pasos exactos que debo seguir en Android Studio y el reporte de la etapa.
````

**Tú:** `npx cap open android` → esperar sincronización de Gradle → elegir dispositivo → **Run ▶**. Si hay errores, usa la plantilla de la sección 9.

### Etapa 3: API en Laravel

````text
La Etapa 2 está validada y la app corre en Android. Procede con la Etapa 3 (sección 8 del plan).
Crea gato-api, la migración, el modelo, el controlador y las rutas.
Prueba la API con curl (POST válido, GET y un POST inválido que devuelva 422) y muéstrame las respuestas.
Deja el servidor corriendo con `php artisan serve --host=0.0.0.0 --port=8000` solo si te lo pido.
Entrega el reporte con los criterios de la sección 8.6.
````

### Etapa 4: Conexión app y Laravel

````text
La Etapa 3 está validada. Procede con la Etapa 4 (sección 9 del plan).
Pregúntame primero si probaré en emulador o en teléfono físico para configurar bien VITE_API_URL.
Implementa el cliente de la API, el guardado al terminar cada partida y la vista de historial.
Aplica en desarrollo la Opción A o la B de la sección 9.3 (dime cuál eliges y por qué).
Entrega el reporte y los pasos de verificación que debo hacer yo en el dispositivo.
````

### Etapa 5 (opcional): Sanctum y vibración

````text
La Etapa 4 está validada. Procede con la Etapa 5 (sección 10 del plan).
Implementa registro, login y rutas protegidas con Sanctum, guarda el token con
@capacitor/preferences y agrega la vibración al ganar con @capacitor/haptics.
Muestra primero tu plan y espera mi aprobación.
````

### Cierre: evaluación de factibilidad

````text
Con todo lo hecho, ayúdame a llenar la tabla de la sección 11 del plan.
Usa solo lo que realmente ocurrió en las pruebas (errores, tiempos, rendimiento)
y dime con honestidad qué riesgos ves para el proyecto real.
````

---

## 9. Plantilla para reportar errores al agente

Cuando algo falle (sobre todo en Android Studio, que el agente no ve), copia esto:

````text
Error en la Etapa [N], paso [descripción].

Qué hice: [comando o acción exacta]
Dónde: [terminal / Android Studio / emulador / teléfono / navegador]
Qué esperaba: [...]
Qué pasó: [...]

Mensaje de error completo (sin recortar):
[pega aquí el texto]

Entorno: [Windows/macOS/Linux], [emulador API xx o modelo de teléfono]

Diagnostica la causa más probable antes de proponer cambios y explícame por qué.
No modifiques archivos hasta que yo apruebe tu propuesta.
````

---

## 10. Buenas prácticas y seguridad

- **Revisa el plan antes de aprobar.** En modo *Planning* el agente presenta un plan; si algo no te convence, corrígelo antes de que escriba código.
- **Lee los comandos antes de aprobarlos**, sobre todo los que instalan paquetes.
- **Commit por etapa.** Si algo sale mal, puedes volver al último estado bueno (`git log`, `git checkout`).
- **Una conversación por etapa** mantiene el contexto limpio; el `AGENTS.md` y el plan le dan al nuevo agente todo lo necesario.
- **No pegues secretos** (`.env`, tokens, claves) en los prompts.
- **No confundas "el agente dice que funciona" con "funciona".** La prueba real es verla correr en el emulador o en el teléfono.
- **Contrasta con el plan:** si el agente propone una versión o configuración distinta a la del plan, pídele la fuente oficial.
- Esta es una prueba desechable: no uses datos reales ni la conectes a servicios de producción.

---

## 11. Checklist de avance

- [ ] Preparación manual completa (Antigravity, Node 22, PHP 8.3, Composer, Git, Android Studio, SDK 36, dispositivo)
- [ ] Workspace creado con `AGENTS.md` y `docs/`
- [ ] Antigravity configurado (planificación, revisión, terminal)
- [ ] Etapa 0 completada y confirmada
- [ ] Etapa 1 completada (juego en navegador) y commit
- [ ] Etapa 2 completada (app corriendo en Android) y commit
- [ ] Etapa 3 completada (API probada con curl) y commit
- [ ] Etapa 4 completada (app y API conectadas) y commit
- [ ] Etapa 5 completada o descartada
- [ ] Evaluación de factibilidad llenada

---

## 12. Referencias

- Documentación de Antigravity: https://antigravity.google/docs
- Guía introductoria de Google (codelab): https://codelabs.developers.google.com/getting-started-google-antigravity
- Capacitor, entorno: https://capacitorjs.com/docs/getting-started/environment-setup
- Laravel 13: https://laravel.com/docs/13.x/installation
