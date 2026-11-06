<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Gato</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding">
      <!-- Mensaje de estado: turno actual, ganador o empate -->
      <h2 class="estado">{{ mensaje }}</h2>

      <!-- Tablero de 3x3 -->
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

      <!-- Botón para reiniciar partida -->
      <ion-button expand="block" @click="reiniciar">Nueva partida</ion-button>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import {
  IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonButton,
} from '@ionic/vue';

// Definición de tipos
type Celda = 'X' | 'O' | null;

// Combinaciones ganadoras posibles en un tablero 3x3
const LINEAS = [
  [0, 1, 2], [3, 4, 5], [6, 7, 8], // Filas
  [0, 3, 6], [1, 4, 7], [2, 5, 8], // Columnas
  [0, 4, 8], [2, 4, 6],             // Diagonales
];

// Estado reactivo: tablero con 9 posiciones vacías y turno de X
const tablero = ref<Celda[]>(Array(9).fill(null));
const turnoX = ref(true);

// Revisa si alguna línea ganadora tiene el mismo símbolo ('X' u 'O')
const ganador = computed<Celda>(() => {
  for (const [a, b, c] of LINEAS) {
    const v = tablero.value[a];
    if (v && v === tablero.value[b] && v === tablero.value[c]) return v;
  }
  return null;
});

// Hay empate si no hay ganador y ya no quedan celdas nulas
const empate = computed(
  () => !ganador.value && tablero.value.every((c) => c !== null)
);

// Texto que se muestra en pantalla según el estado
const mensaje = computed(() => {
  if (ganador.value) return `Ganó ${ganador.value}`;
  if (empate.value) return 'Empate';
  return `Turno de ${turnoX.value ? 'X' : 'O'}`;
});

// Acción al presionar una celda
function jugar(i: number) {
  // Ignora el clic si la casilla ya tiene valor o si el juego ya terminó
  if (tablero.value[i] || ganador.value) return;
  tablero.value[i] = turnoX.value ? 'X' : 'O';
  turnoX.value = !turnoX.value;
}

// Reinicia el tablero a su estado inicial
function reiniciar() {
  tablero.value = Array(9).fill(null);
  turnoX.value = true;
}
</script>

<style scoped>
.estado { 
  text-align: center; 
}

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