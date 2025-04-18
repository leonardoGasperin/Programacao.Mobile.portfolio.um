<template>
  <ion-page>
    <ion-header>
      <ion-toolbar color="primary">
        <ion-title class="ion-text-center">Histórico IMC</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true" class="ion-padding">
      <div class="content-container">
        <ion-card class="filter-card">
          <ion-card-content>
            <form @submit.prevent="applyFilter" class="filter-form">
              <ion-item>
                <ion-label position="floating">IMC</ion-label>
                <ion-input v-model="filters.imc" class="custom-input" type="number" step="0.01"></ion-input>
              </ion-item>
              <ion-item>
                <ion-label position="floating">Peso (kg)</ion-label>
                <ion-input v-model="filters.weight" class="custom-input" type="number" step="0.1"></ion-input>
              </ion-item>
              <ion-item>
                <ion-label position="floating">Altura (m)</ion-label>
                <ion-input v-model="filters.height" class="custom-input" type="number" step="0.01"></ion-input>
              </ion-item>
              <ion-item>
                <ion-label position="floating">Data (MM/DD/YYYY)</ion-label>
                <ion-input v-model="filters.date" class="custom-input" type="date"></ion-input>
              </ion-item>
              <div class="button-group">
                <ion-button expand="block" type="submit" class="filter-button">
                  Filtrar
                </ion-button>
                <ion-button expand="block" fill="outline" type="button" class="clear-button" @click="clearFilters">
                  Limpar
                </ion-button>
              </div>
            </form>
          </ion-card-content>
        </ion-card>

        <!-- History Card -->
        <ion-card class="history-card">
          <ion-card-content>
            <ion-list v-if="filteredHistory.length > 0" class="centered-list">
              <ion-item v-for="(item, index) in filteredHistory" :key="index" class="history-item">
                <ion-label class="ion-text-center">
                  <h2 class="history-imc">IMC: {{ item.imc.toFixed(2) }}</h2>
                  <p class="history-details">Peso: {{ item.weight }}kg | Altura: {{ item.height }}m</p>
                  <p class="history-date">Data: {{ new Date(item.date).toLocaleDateString() }}</p>
                  <p class="history-classification">Classificação: {{ item.classification }}</p>
                </ion-label>
              </ion-item>
            </ion-list>
            <div v-else class="ion-text-center ion-padding">
              <p>Nenhum histórico disponível</p>
            </div>
          </ion-card-content>
        </ion-card>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, computed } from 'vue';
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
  IonLabel,
  IonInput,
  IonButton
} from '@ionic/vue';
import { emitter } from '../eventBus';

interface HistoryItem {
  imc: number;
  weight: number;
  height: number;
  classification: string;
  date: string;
}

const filters = ref({
  imc: '',
  weight: '',
  height: '',
  date: ''
});

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

const filteredHistory = computed(() => {
  return history.value.filter(item => {
    let matches = true;
    
    if (filters.value.imc && filters.value.imc !== '') {
      matches = matches && item.imc.toFixed(2).includes(filters.value.imc);
    }
    
    if (filters.value.weight && filters.value.weight !== '') {
      matches = matches && item.weight.toString().includes(filters.value.weight);
    }
    
    if (filters.value.height && filters.value.height !== '') {
      matches = matches && item.height.toString().includes(filters.value.height);
    }
    
    if (filters.value.date && filters.value.date !== '') {
      const filterDate = new Date(filters.value.date).toLocaleDateString();
      const itemDate = new Date(item.date).toLocaleDateString();
      matches = matches && itemDate === filterDate;
    }
    
    return matches;
  });
});

const applyFilter = () => {
};

const clearFilters = () => {
  filters.value = {
    imc: '',
    weight: '',
    height: '',
    date: ''
  };
};

onMounted(() => {
  loadHistory();
  emitter.on('newImcCalculation', loadHistory);
});

onUnmounted(() => {
  emitter.off('newImcCalculation', loadHistory);
});
</script>


<style scoped>
.content-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  padding: 16px;
  gap: 20px;
}

.history-card {
  max-width: 600px;
  width: 100%;
}

.centered-list {
  width: 100%;
}

.history-item {
  --padding-start: 0;
  --inner-padding-end: 0;
}

ion-item::part(native) {
  justify-content: center;
}

.history-imc {
  font-size: 1.2em;
  font-weight: bold;
  margin-bottom: 8px;
}

.history-details, .history-date, .history-classification {
  margin: 4px 0;
}

.filter-card {
  max-width: 600px;
  width: 100%;
  margin-bottom: 0;
}

.filter-form {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.filter-button {
  margin-top: 16px;
}

ion-item {
  --padding-start: 0;
  --inner-padding-end: 0;
  margin-bottom: 8px;
}
.custom-input {
  --padding-start: 3px;
  --padding-top: 25px;
}
</style>
