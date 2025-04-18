<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Perfil do Usuário</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Perfil do Usuário</ion-title>
        </ion-toolbar>
      </ion-header>

      <div class="profile-container" style="display: flex; justify-content: center; padding: 20px;">
        <ion-card style="width: 100%; max-width: 400px; text-align: center;">
          <ion-card-header>
            <ion-avatar style="margin: 0 auto; width: 100px; height: 100px;">
              <img src="https://placehold.co/100" alt="Avatar do Usuário" />
            </ion-avatar>
            <ion-card-title style="margin-top: 10px;">Nome do Usuário</ion-card-title>
            <ion-card-subtitle>Usuário Premium</ion-card-subtitle>
          </ion-card-header>
          <ion-card-content>
            <p><strong>Email:</strong> usuario@example.com</p>
            <p><strong>Telefone:</strong> (11) 99999-9999</p>
            <p><strong>Bio:</strong> Uma breve descrição sobre o usuário que pode incluir interesses e outras informações relevantes.</p>
          </ion-card-content>
        </ion-card>
      </div>

    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonCard, IonCardHeader, IonCardTitle, IonCardSubtitle, IonCardContent, IonAvatar } from '@ionic/vue';
import ExploreContainer from '@/components/ExploreContainer.vue';
</script>
