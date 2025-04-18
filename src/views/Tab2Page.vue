<template>
  <ion-page>
    <ion-header>
      <ion-toolbar color="primary">
        <ion-title class="ion-text-center">Histórico IMC</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true" class="ion-padding">
      <ion-card class="history-card">
        <ion-card-content>
          <ion-list v-if="history.length > 0">
            <ion-item v-for="(item, index) in history" :key="index" class="history-item">
              <ion-label>
                <h2 class="history-imc">IMC: {{ item.imc.toFixed(2) }}</h2>
                <p class="history-details">Peso: {{ item.weight }}kg | Altura: {{ item.height }}m</p>
                <p class="history-date">{{ new Date(item.date).toLocaleDateString() }}</p>
                <p class="history-classification">{{ item.classification }}</p>
              </ion-label>
            </ion-item>
          </ion-list>
          <div v-else class="ion-text-center ion-padding">
            <p>Nenhum histórico disponível</p>
          </div>
        </ion-card-content>
      </ion-card>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';
import { Storage } from '@capacitor/storage';
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonCard,
  IonCardContent,
  IonList,
  IonItem,
  IonLabel
} from '@ionic/vue';
import { emitter } from '../eventBus';

interface HistoryItem {
  imc: number;
  weight: number;
  height: number;
  classification: string;
  date: string;
}

const history = ref<HistoryItem[]>([]);

const loadHistory = async () => {
  try {
    const { value } = await Storage.get({ key: 'imc-history' });
    if (value) {
      history.value = JSON.parse(value);
    }
  } catch (error) {
    console.error('Error loading history:', error);
  }
};

// Listen for new IMC calculations
onMounted(() => {
  loadHistory();
  emitter.on('newImcCalculation', loadHistory);
});

// Clean up event listener
onUnmounted(() => {
  emitter.off('newImcCalculation', loadHistory);
});
</script>
