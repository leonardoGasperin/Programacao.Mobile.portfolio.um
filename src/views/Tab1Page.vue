<template>
  <ion-page>
    <ion-header>
      <ion-toolbar color="primary">
        <ion-title class="ion-text-center">Calculadora IMC</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true" class="ion-padding">
      <div class="calculator-container">
        <ion-card class="main-card">
          <ion-card-content>
            <ion-item lines="full">
              <ion-label position="floating">Peso (kg)</ion-label>
              <ion-input type="number" v-model="weight" inputmode="decimal" class="custom-input"></ion-input>
            </ion-item>

            <ion-item lines="full">
              <ion-label position="floating">Altura (m)</ion-label>
              <ion-input type="number" v-model="height" inputmode="decimal" class="custom-input"></ion-input>
            </ion-item>

            <ion-button expand="block" class="calculate-button" @click="calculateIMC">
              Calcular IMC
            </ion-button>

            <div v-if="result" class="result-container">
              <h2 class="result-title">Seu IMC: {{ result.imc.toFixed(2) }}</h2>
              <p class="result-classification">{{ result.classification }}</p>
            </div>
          </ion-card-content>
        </ion-card>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonCard,
  IonCardContent,
  IonItem,
  IonLabel,
  IonInput,
  IonButton
} from '@ionic/vue';
import { Storage } from '@capacitor/storage';
import { emitter } from '../eventBus';

const weight = ref('');
const height = ref('');
const result = ref<IMCResult | null>(null);

interface IMCResult {
  imc: number;
  classification: string;
}

interface HistoryItem {
  imc: number;
  weight: number;
  height: number;
  classification: string;
  date: string;
}

const saveToHistory = async (result: IMCResult) => {
  try {
    // Get existing history
    const { value } = await Storage.get({ key: 'imc-history' });
    const history: HistoryItem[] = value ? JSON.parse(value) : [];

    // Add new entry
    const newEntry: HistoryItem = {
      imc: result.imc,
      weight: parseFloat(weight.value),
      height: parseFloat(height.value),
      classification: result.classification,
      date: new Date().toISOString()
    };

    history.unshift(newEntry); // Add to beginning of array
    
    // Save updated history
    await Storage.set({
      key: 'imc-history',
      value: JSON.stringify(history)
    });
    emitter.emit('newImcCalculation');
  } catch (error) {
    console.error('Error saving to history:', error);
  }
};

const calculateIMC = async () => {
  const w = parseFloat(weight.value);
  const h = parseFloat(height.value);
  if (!w || !h) return;
  
  const imc = w / (h * h);
  let classification = '';
  
  if (imc < 18.5) classification = 'Abaixo do peso';
  else if (imc < 25) classification = 'Peso normal';
  else if (imc < 30) classification = 'Sobrepeso';
  else classification = 'Obesidade';

  result.value = { imc, classification };
  
  // Save to history after calculating
  await saveToHistory(result.value);
};
</script>

<style scoped>
.calculator-container {
  max-width: 600px;
  margin: 0 auto;
}

.main-card {
  margin: 16px;
  border-radius: 16px;
}

.calculate-button {
  margin-top: 20px;
}

.result-container {
  margin-top: 20px;
  text-align: center;
}

.result-title {
  color: var(--ion-color-primary);
  font-size: 24px;
  margin-bottom: 8px;
}

.result-classification {
  font-size: 18px;
  color: var(--ion-color-dark);
}
</style>