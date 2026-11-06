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
