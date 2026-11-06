# Plan de prueba de concepto: App Android "Gato" (Tic Tac Toe)

**Stack a validar:** Vue 3 + Ionic + Capacitor + Android Studio + Laravel (API REST)
**Fecha del documento:** 04/11/2026
**Estado:** Borrador para revisión. Nada se ha instalado ni ejecutado todavía.

> Las versiones y requisitos de este documento se verificaron contra la documentación oficial de Capacitor y Laravel en la fecha indicada. Antes de instalar, confirma que siguen vigentes (ver sección de referencias).

---

## 1. Objetivo y alcance

### Objetivo
Comprobar, con un proyecto pequeño y desechable, que la arquitectura **Vue + Ionic + Capacitor + Laravel** es viable para construir una app Android, antes de invertir en el proyecto real.

### Preguntas de factibilidad que debe responder la prueba

| # | Pregunta | Se responde en |
|---|---|---|
| 1 | ¿Puedo construir la interfaz con Vue + Ionic y que funcione en el navegador? | Etapa 1 |
| 2 | ¿Puedo empaquetarla con Capacitor, abrirla en Android Studio y correrla en emulador y/o teléfono real? | Etapa 2 |
| 3 | ¿Se comunica bien la app (en el teléfono) con un backend Laravel? | Etapas 3 y 4 |
| 4 | ¿Funciona la autenticación con tokens (Sanctum) y el acceso a una función nativa del teléfono? | Etapa 5 (opcional) |

### Dentro del alcance
- Juego de gato para dos jugadores en el mismo dispositivo.
- Guardar el resultado de cada partida en Laravel y mostrar el historial.
- (Opcional) Login con Sanctum y vibración al ganar.

### Fuera del alcance
- Publicación en Google Play.
- Diseño visual elaborado.
- Modo offline con sincronización.
- Juego en línea entre dos teléfonos.
- iOS.

---

## 2. Arquitectura

```
┌─────────────────────────────┐          HTTP / JSON          ┌──────────────────────┐
│  Teléfono Android           │  ───────────────────────────► │  Servidor Laravel    │
│                             │                               │  (API REST)          │
│  ┌───────────────────────┐  │  ◄─────────────────────────── │                      │
│  │ Capacitor (contenedor │  │                               │  ┌────────────────┐  │
│  │ nativo + WebView)     │  │                               │  │ Base de datos  │  │
│  │  ┌─────────────────┐  │  │                               │  │ (SQLite/MySQL) │  │
│  │  │ App Vue + Ionic │  │  │                               │  └────────────────┘  │
│  │  └─────────────────┘  │  │                               └──────────────────────┘
│  └───────────────────────┘  │
└─────────────────────────────┘
```

**Puntos clave:**
- Laravel **no corre dentro de la app**: corre en un servidor (en pruebas, tu propia computadora).
- La app es una aplicación web (Vue) ejecutándose dentro de un WebView de Android, empaquetada por Capacitor.
- La app y Laravel solo se comunican por HTTP y JSON.

---

## 3. Glosario

| Término | Qué es |
|---|---|
| **Vue** | Framework de JavaScript para construir interfaces. |
| **Ionic** | Librería de componentes de interfaz con aspecto móvil (botones, listas, páginas, navegación) que se integra con Vue. |
| **Capacitor** | Herramienta que envuelve una app web en un proyecto nativo de Android (o iOS) y ofrece plugins para usar funciones del teléfono. |
| **WebView** | Componente de Android que funciona como un "mini navegador" incrustado dentro de una app. |
| **Plugin de Capacitor** | Puente entre JavaScript y el código nativo (cámara, GPS, vibración, almacenamiento…). |
| **Android Studio** | IDE oficial de Google. Aquí se compila la app, se administran emuladores y se genera el APK/AAB. |
| **Android SDK** | Conjunto de herramientas y librerías de Android necesarias para compilar. Se instala desde Android Studio. |
| **Emulador (AVD)** | Teléfono virtual que corre en tu computadora. |
| **API REST** | Forma de comunicación entre programas por HTTP: la app pide o envía datos y el servidor responde (normalmente JSON). |
| **Laravel** | Framework de PHP para el backend. |
| **Sanctum** | Paquete oficial de Laravel para autenticación con tokens, pensado para SPAs y apps móviles. |
| **Token** | Cadena larga que la app guarda tras iniciar sesión y envía en cada petición para identificarse. |
| **CORS** | Mecanismo del navegador/WebView que decide si una página puede llamar a un servidor de otro origen. |
| **APK / AAB** | Archivo instalable de Android. El AAB es el formato que pide Google Play. |

---

## 4. Requisitos del entorno

### 4.1 Frontend y empaquetado (Etapas 1 y 2)

| Herramienta | Requisito | Notas |
|---|---|---|
| **Node.js** | 22 o superior (LTS recomendado) | Requisito de Capacitor 8. |
| **npm** | Incluido con Node | |
| **Ionic CLI** | Última versión | `npm install -g @ionic/cli` |
| **Capacitor** | 8.x | Se instala con el proyecto. |
| **Android Studio** | "Otter" 2025.2.1 o superior | Requisito mínimo de Capacitor 8. |
| **Android SDK** | API 36 (compilación y target), mínimo API 24 | Se instala desde *Tools → SDK Manager*. |
| **JDK** | Usar el que trae Android Studio | No hace falta instalar uno aparte. |
| **Git** | Cualquier versión reciente | Recomendado. |
| **Editor** | VS Code (extensión *Vue - Official*) o similar | |

**Hardware sugerido para el emulador:** 16 GB de RAM recomendados (8 GB como mínimo), SSD y virtualización habilitada en BIOS/UEFI.

### 4.2 Backend (Etapas 3 a 5)

| Herramienta | Requisito | Notas |
|---|---|---|
| **PHP** | 8.3 o superior (Laravel 13 soporta 8.3 a 8.5) | Si usas Laravel 12, basta PHP 8.2. |
| **Composer** | 2.x | Gestor de paquetes de PHP. |
| **Laravel** | 13.x (versión actual) | |
| **Base de datos** | SQLite (más simple para la prueba) | MySQL o PostgreSQL también sirven. |
| **Extensiones PHP** | Las habituales de Laravel (mbstring, openssl, pdo, pdo_sqlite, etc.) | |

### 4.3 Dispositivo de prueba (elige al menos uno)
- **Emulador** creado en Android Studio (*Device Manager*), con una imagen de sistema de API 33 o superior.
- **Teléfono físico** con *Opciones de desarrollador* y *Depuración USB* activadas.

---

## 5. Verificación del entorno (antes de empezar)

Ejecuta estos comandos y anota los resultados:

```bash
node --version        # debe ser v22.x o superior
npm --version
git --version
php --version         # debe ser 8.3 o superior (para Laravel 13)
composer --version
```

En Android Studio:
1. *Settings → Languages & Frameworks → Android SDK*: confirmar que está instalado **Android SDK Platform 36** y las *SDK Tools* (Build-Tools, Platform-Tools, Emulator).
2. *Tools → Device Manager*: crear un emulador (por ejemplo, Pixel con API 36) o conectar un teléfono físico.

**Variables de entorno recomendadas** (si Android Studio no las configura solo):

```
ANDROID_HOME = <ruta al Android SDK>
PATH += <ANDROID_HOME>/platform-tools
```

Rutas habituales del SDK:
- Windows: `C:\Users\<usuario>\AppData\Local\Android\Sdk`
- macOS: `~/Library/Android/sdk`
- Linux: `~/Android/Sdk`

---

## 6. Etapa 1: Frontend del juego (Vue + Ionic, solo navegador)

**Pregunta que responde:** ¿puedo construir la interfaz con Vue + Ionic?

### 6.1 Crear el proyecto

```bash
npm install -g @ionic/cli
ionic start gato-app blank --type=vue --capacitor
cd gato-app
```

Si el asistente pregunta por integrar Capacitor, acepta. Si lo omites, se agrega en la Etapa 2.

### 6.2 Probar que arranca

```bash
ionic serve
```

Se abre el navegador (normalmente en `http://localhost:8100`). Debes ver la página de inicio de la plantilla.

### 6.3 Componente del juego

Reemplaza el contenido de `src/views/HomePage.vue` por:

```vue
<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Gato</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding">
      <h2 class="estado">{{ mensaje }}</h2>

      <div class="tablero">
        <button
          v-for="(celda, i) in tablero"
          :key="i"
          class="celda"
          @click="jugar(i)"
        >
          {{ celda }}
        </button>
      </div>

      <ion-button expand="block" @click="reiniciar">Nueva partida</ion-button>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import {
  IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonButton,
} from '@ionic/vue';

type Celda = 'X' | 'O' | null;

const LINEAS = [
  [0, 1, 2], [3, 4, 5], [6, 7, 8],
  [0, 3, 6], [1, 4, 7], [2, 5, 8],
  [0, 4, 8], [2, 4, 6],
];

const tablero = ref<Celda[]>(Array(9).fill(null));
const turnoX = ref(true);

const ganador = computed<Celda>(() => {
  for (const [a, b, c] of LINEAS) {
    const v = tablero.value[a];
    if (v && v === tablero.value[b] && v === tablero.value[c]) return v;
  }
  return null;
});

const empate = computed(
  () => !ganador.value && tablero.value.every((c) => c !== null)
);

const mensaje = computed(() => {
  if (ganador.value) return `Ganó ${ganador.value}`;
  if (empate.value) return 'Empate';
  return `Turno de ${turnoX.value ? 'X' : 'O'}`;
});

function jugar(i: number) {
  if (tablero.value[i] || ganador.value) return;
  tablero.value[i] = turnoX.value ? 'X' : 'O';
  turnoX.value = !turnoX.value;
}

function reiniciar() {
  tablero.value = Array(9).fill(null);
  turnoX.value = true;
}
</script>

<style scoped>
.estado { text-align: center; }
.tablero {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 8px;
  max-width: 320px;
  margin: 16px auto;
}
.celda {
  aspect-ratio: 1;
  font-size: 2.5rem;
  font-weight: bold;
  border: 2px solid var(--ion-color-medium);
  background: transparent;
  color: var(--ion-text-color);
}
</style>
```

### 6.4 Criterios de éxito de la Etapa 1
- [ ] `ionic serve` arranca sin errores.
- [ ] Se pueden hacer jugadas alternando X y O.
- [ ] Se detecta correctamente victoria y empate.
- [ ] El botón "Nueva partida" reinicia el tablero.
- [ ] La vista se ve bien en modo móvil (herramientas de desarrollador del navegador, modo dispositivo).

---

## 7. Etapa 2: Empaquetar con Capacitor y correr en Android

**Pregunta que responde:** ¿funciona el flujo Ionic + Capacitor + Android Studio en mi equipo?

### 7.1 Configurar Capacitor

Revisa `capacitor.config.ts` en la raíz del proyecto:

```ts
import type { CapacitorConfig } from '@capacitor/cli';

const config: CapacitorConfig = {
  appId: 'com.ejemplo.gato',   // identificador único, formato dominio inverso
  appName: 'Gato',
  webDir: 'dist',              // carpeta donde Vue deja el build
};

export default config;
```

> `appId` no se puede cambiar fácilmente una vez publicada la app. Para la prueba no importa; para el proyecto real conviene definirlo con cuidado.

### 7.2 Agregar la plataforma Android

```bash
ionic build                    # genera la carpeta dist/
npx cap add android            # crea la carpeta android/ (proyecto nativo)
npx cap sync android           # copia dist/ y plugins al proyecto nativo
```

### 7.3 Abrir en Android Studio y ejecutar

```bash
npx cap open android
```

En Android Studio:
1. Esperar a que termine la sincronización de Gradle (la primera vez puede tardar varios minutos y descarga dependencias).
2. Elegir el dispositivo (emulador o teléfono) en la barra superior.
3. Pulsar **Run ▶**.

**Alternativa por línea de comandos** (compila y lanza sin abrir Android Studio):

```bash
ionic cap run android
```

**Modo desarrollo con recarga en vivo** (útil para iterar rápido):

```bash
ionic cap run android -l --external
```

### 7.4 Flujo de trabajo tras cada cambio en el código

```bash
ionic build
npx cap sync android
# luego Run en Android Studio, o: ionic cap run android
```

### 7.5 Criterios de éxito de la Etapa 2
- [ ] El proyecto de Android Studio sincroniza Gradle sin errores.
- [ ] La app se instala y abre en el emulador y/o teléfono.
- [ ] El juego funciona igual que en el navegador.
- [ ] Se entiende el ciclo *build → sync → run*.

---

## 8. Etapa 3: API en Laravel (probada de forma independiente)

**Pregunta que responde:** ¿puedo levantar un backend que guarde y devuelva partidas?

### 8.1 Crear el proyecto

```bash
composer create-project laravel/laravel gato-api
cd gato-api
```

> Si usas el instalador global (`laravel new gato-api`), cuando pregunte por un starter kit elige **ninguno**, porque solo se necesita API.

La base de datos por defecto es SQLite y suele quedar lista. Ejecuta las migraciones iniciales si el instalador no lo hizo:

```bash
php artisan migrate
```

### 8.2 Habilitar rutas de API

```bash
php artisan install:api
```

Este comando crea `routes/api.php` e instala Laravel Sanctum con su migración. En esta etapa **no** se usa Sanctum; queda preparado para la Etapa 5.

### 8.3 Modelo, migración y controlador

```bash
php artisan make:model Partida -m
php artisan make:controller PartidaController
```

**Migración** (`database/migrations/xxxx_create_partidas_table.php`), dentro de `up()`:

```php
Schema::create('partidas', function (Blueprint $table) {
    $table->id();
    $table->string('ganador');   // 'X', 'O' o 'empate'
    $table->json('tablero');     // arreglo de 9 posiciones
    $table->timestamps();
});
```

**Modelo** (`app/Models/Partida.php`):

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Partida extends Model
{
    protected $fillable = ['ganador', 'tablero'];

    protected $casts = [
        'tablero' => 'array',
    ];
}
```

**Controlador** (`app/Http/Controllers/PartidaController.php`):

```php
<?php

namespace App\Http\Controllers;

use App\Models\Partida;
use Illuminate\Http\Request;

class PartidaController extends Controller
{
    public function index()
    {
        return Partida::latest()->take(20)->get();
    }

    public function store(Request $request)
    {
        $datos = $request->validate([
            'ganador'   => ['required', 'in:X,O,empate'],
            'tablero'   => ['required', 'array', 'size:9'],
            'tablero.*' => ['nullable', 'in:X,O'],
        ]);

        $partida = Partida::create($datos);

        return response()->json($partida, 201);
    }
}
```

**Rutas** (`routes/api.php`):

```php
<?php

use App\Http\Controllers\PartidaController;
use Illuminate\Support\Facades\Route;

Route::get('/partidas', [PartidaController::class, 'index']);
Route::post('/partidas', [PartidaController::class, 'store']);
```

### 8.4 Ejecutar migraciones y levantar el servidor

```bash
php artisan migrate
php artisan serve --host=0.0.0.0 --port=8000
```

`--host=0.0.0.0` permite que otros dispositivos de la red (como tu teléfono) lleguen al servidor.

### 8.5 Probar la API sin la app

```bash
# Guardar una partida
curl -X POST http://localhost:8000/api/partidas \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"ganador":"X","tablero":["X","O","X",null,"X","O",null,null,"O"]}'

# Listar partidas
curl http://localhost:8000/api/partidas -H "Accept: application/json"
```

> Es importante enviar el encabezado `Accept: application/json`; sin él, Laravel puede responder con páginas HTML de error en lugar de JSON.

### 8.6 Criterios de éxito de la Etapa 3
- [ ] `php artisan serve` arranca sin errores.
- [ ] El `POST` devuelve código 201 y la partida guardada.
- [ ] El `GET` devuelve la lista en JSON.
- [ ] Un `POST` con datos inválidos devuelve error de validación (422).

---

## 9. Etapa 4: Conectar la app con Laravel

**Pregunta que responde:** ¿se comunican bien el teléfono y el backend?

### 9.1 Direcciones según dónde corra la app

| La app corre en… | URL base de la API |
|---|---|
| Navegador (`ionic serve`) | `http://localhost:8000/api` |
| **Emulador de Android** | `http://10.0.2.2:8000/api` |
| **Teléfono físico** (misma red Wi-Fi) | `http://<IP-de-tu-PC>:8000/api` |

> En el emulador, `localhost` apunta al propio emulador, no a tu computadora. La dirección especial `10.0.2.2` es el alias hacia tu PC.
> Para el teléfono físico, averigua la IP local de tu PC (`ipconfig` en Windows, `ifconfig` o `ip a` en macOS/Linux) y verifica que el firewall permita el puerto 8000.

### 9.2 Variable de entorno en el proyecto Vue

Archivo `.env` en la raíz de `gato-app` (cámbialo según el dispositivo que uses):

```
VITE_API_URL=http://10.0.2.2:8000/api
```

Después de cambiarla hay que volver a ejecutar `ionic build` y `npx cap sync android`.

### 9.3 Permitir tráfico HTTP en desarrollo (importante)

Android bloquea por defecto el tráfico HTTP sin cifrar, y el WebView de Capacitor sirve la app como `https://localhost`, lo que provoca bloqueo por contenido mixto al llamar a una API en `http://`. Para **desarrollo**, hay dos opciones:

**Opción A: ajustar la configuración del servidor de Capacitor** (`capacitor.config.ts`):

```ts
const config: CapacitorConfig = {
  appId: 'com.ejemplo.gato',
  appName: 'Gato',
  webDir: 'dist',
  server: {
    androidScheme: 'http',
    cleartext: true,
  },
};
```

**Opción B: usar el HTTP nativo de Capacitor**, que evita restricciones de CORS y de contenido mixto:

```ts
plugins: {
  CapacitorHttp: { enabled: true },
},
```

> Ambas son solo para desarrollo. En producción la API debe servirse por **HTTPS** y estas configuraciones deben retirarse.
> Cuál de las dos funciona mejor puede variar según el entorno; la prueba sirve justamente para comprobarlo.

### 9.4 CORS en Laravel

Laravel ya trae CORS habilitado para las rutas `api/*` y, por defecto, acepta cualquier origen, lo que es suficiente para la prueba. Si más adelante se quiere restringir:

```bash
php artisan config:publish cors
```

y editar `config/cors.php`, por ejemplo con estos orígenes de desarrollo: `http://localhost:8100` (navegador con Ionic) y `https://localhost` o `http://localhost` (WebView de Capacitor, según el `androidScheme`).

### 9.5 Cliente de API en Vue

Crear `src/api/partidas.ts`:

```ts
const BASE = import.meta.env.VITE_API_URL;

export interface Partida {
  id?: number;
  ganador: 'X' | 'O' | 'empate';
  tablero: (string | null)[];
  created_at?: string;
}

export async function guardarPartida(p: Partida): Promise<Partida> {
  const r = await fetch(`${BASE}/partidas`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Accept: 'application/json',
    },
    body: JSON.stringify(p),
  });
  if (!r.ok) throw new Error(`Error ${r.status}`);
  return r.json();
}

export async function listarPartidas(): Promise<Partida[]> {
  const r = await fetch(`${BASE}/partidas`, {
    headers: { Accept: 'application/json' },
  });
  if (!r.ok) throw new Error(`Error ${r.status}`);
  return r.json();
}
```

### 9.6 Integrar en el juego

En `HomePage.vue`:
1. Importar `guardarPartida` y `watch`.
2. Cuando el juego termine (hay ganador o empate), llamar a `guardarPartida` con el resultado y el tablero.
3. Agregar una segunda vista (o una sección debajo) que llame a `listarPartidas()` y muestre el historial con `ion-list`.

Esqueleto de la lógica de guardado:

```ts
import { watch } from 'vue';
import { guardarPartida } from '@/api/partidas';

watch([ganador, empate], async () => {
  if (!ganador.value && !empate.value) return;
  try {
    await guardarPartida({
      ganador: ganador.value ?? 'empate',
      tablero: tablero.value,
    });
  } catch (e) {
    console.error('No se pudo guardar la partida', e);
  }
});
```

### 9.7 Criterios de éxito de la Etapa 4
- [ ] Al terminar una partida en el emulador/teléfono, aparece un nuevo registro en la base de datos de Laravel.
- [ ] La pantalla de historial muestra las partidas guardadas.
- [ ] Se identificó qué configuración de red (Opción A o B) funciona en tu entorno.

---

## 10. Etapa 5 (opcional): Sanctum y un plugin nativo

**Pregunta que responde:** ¿funciona la autenticación con tokens y el acceso a funciones nativas?

### 10.1 Autenticación con Sanctum (resumen)

1. Verificar que el modelo `User` use el trait `HasApiTokens` (`use Laravel\Sanctum\HasApiTokens;`).
2. Crear rutas `POST /api/register` y `POST /api/login`. El login valida credenciales y devuelve un token:

```php
$token = $user->createToken('movil')->plainTextToken;
return response()->json(['token' => $token]);
```

3. Proteger las rutas de partidas:

```php
Route::middleware('auth:sanctum')->group(function () {
    Route::get('/partidas', [PartidaController::class, 'index']);
    Route::post('/partidas', [PartidaController::class, 'store']);
});
```

4. (Opcional) Agregar `user_id` a la tabla `partidas` para que cada usuario vea solo las suyas.
5. En la app: guardar el token y enviarlo en cada petición:

```
Authorization: Bearer <token>
```

Para guardar el token en el dispositivo, usar el plugin de Capacitor Preferences:

```bash
npm install @capacitor/preferences
npx cap sync android
```

### 10.2 Plugin nativo: vibración al ganar

```bash
npm install @capacitor/haptics
npx cap sync android
```

```ts
import { Haptics, ImpactStyle } from '@capacitor/haptics';

// Al detectar un ganador:
await Haptics.impact({ style: ImpactStyle.Heavy });
```

### 10.3 Criterios de éxito de la Etapa 5
- [ ] Registro e inicio de sesión funcionan desde la app.
- [ ] Una petición sin token recibe 401.
- [ ] El token persiste al cerrar y reabrir la app.
- [ ] El teléfono vibra al ganar (en teléfono físico; el emulador puede no mostrarlo).

---

## 11. Evaluación final de factibilidad

Al terminar las etapas, responde con base en la experiencia real:

| Criterio | Resultado (llenar) |
|---|---|
| Facilidad de configuración del entorno (1-5) | |
| Fluidez y rendimiento de la app en el teléfono real | |
| Tiempo del ciclo "cambio → ver resultado" | |
| Estabilidad de la comunicación con Laravel | |
| Facilidad para usar plugins nativos | |
| ¿Se cumplen los requisitos del proyecto real con este stack? | |

**Señales de que conviene considerar otra tecnología:**
- Animaciones o interacciones que se sienten lentas en un teléfono de gama media.
- Necesidad de funciones nativas que no tengan plugin maduro.
- El proyecto real exige trabajo offline complejo o procesamiento intensivo.

---

## 12. Problemas comunes y soluciones

| Síntoma | Causa probable | Solución |
|---|---|---|
| `SDK location not found` al compilar | Android Studio no encuentra el SDK | Definir `ANDROID_HOME` o crear `android/local.properties` con `sdk.dir=<ruta>` |
| Gradle falla o tarda mucho la primera vez | Descarga de dependencias | Esperar; verificar conexión a internet |
| La app abre en blanco | `webDir` incorrecto o falta `ionic build` | Confirmar `webDir: 'dist'`, ejecutar `ionic build` y `npx cap sync android` |
| Cambios en el código no aparecen | Faltó build o sync | Repetir `ionic build` + `npx cap sync android` |
| `Network Error` desde el emulador | Se usa `localhost` | Usar `http://10.0.2.2:8000/api` |
| `Network Error` desde teléfono físico | IP incorrecta, firewall o distinta red | Verificar IP, red Wi-Fi y que `artisan serve` use `--host=0.0.0.0` |
| Error de contenido mixto o petición bloqueada | API en `http://` con la app en `https://localhost` | Aplicar Opción A u Opción B de la sección 9.3 |
| Error CORS en el navegador | Origen no permitido | Revisar `config/cors.php` |
| Laravel devuelve HTML en lugar de JSON en errores | Falta `Accept: application/json` | Enviar siempre ese encabezado |
| Emulador muy lento o no arranca | Virtualización desactivada | Habilitar virtualización en BIOS/UEFI; revisar aceleración en Android Studio |
| Teléfono no aparece en Android Studio | Depuración USB desactivada o cable solo de carga | Activar *Depuración USB*, aceptar el aviso en el teléfono, usar un cable de datos |
| Error por versión de Node | Node menor a 22 | Actualizar Node |

---

## 13. Estructura final esperada

```
proyectos/
├── gato-app/                  # Frontend (Vue + Ionic + Capacitor)
│   ├── android/               # Proyecto nativo (se abre en Android Studio)
│   ├── src/
│   │   ├── api/partidas.ts
│   │   └── views/HomePage.vue
│   ├── capacitor.config.ts
│   └── .env
└── gato-api/                  # Backend (Laravel)
    ├── app/Http/Controllers/PartidaController.php
    ├── app/Models/Partida.php
    ├── database/migrations/
    └── routes/api.php
```

---

## 14. Pendientes por definir (para revisar contigo)

1. Sistema operativo de tu equipo de desarrollo (Windows, macOS o Linux), para ajustar comandos y rutas.
2. Dispositivo de prueba: emulador, teléfono físico o ambos.
3. Si usas TypeScript o JavaScript en Vue (el documento asume TypeScript, que es el valor por defecto de la plantilla de Ionic).
4. Base de datos: SQLite (más simple) o MySQL/PostgreSQL.
5. Alcance: ¿incluimos la Etapa 5 (Sanctum y vibración) o la dejamos para después?
6. Si ya tienes instalado alguno de los requisitos de la sección 4.

---

## 15. Referencias oficiales

- Capacitor, configuración del entorno: https://capacitorjs.com/docs/getting-started/environment-setup
- Capacitor, actualización a la versión 8 (requisitos): https://capacitorjs.com/docs/updating/8-0
- Ionic Framework con Vue: https://ionicframework.com/docs/vue/overview
- Laravel, notas de la versión 13 (requisitos de PHP): https://laravel.com/docs/13.x/releases
- Laravel, instalación: https://laravel.com/docs/13.x/installation
- Laravel Sanctum: https://laravel.com/docs/13.x/sanctum
- Android Studio: https://developer.android.com/studio
