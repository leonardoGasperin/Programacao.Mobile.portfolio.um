<template>
  <ion-page>
    <ion-header>
      <ion-toolbar color="primary">
        <ion-title class="ion-text-center">Perfil do Usuário</ion-title>
        <ion-buttons slot="end">
          <ion-button @click="openEditModal">Editar Perfil</ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>
    
    <ion-content :fullscreen="true" class="ion-padding">
      <div class="profile-container">
        <ion-card class="main-card">
          <ion-card-content >
            <div class="profile-header">
              <ion-avatar class="profile-avatar">
                <img :src="profileImage || 'https://placehold.co/100'" alt="Avatar do Usuário" />
              </ion-avatar>
              <h2 class="profile-name">{{ name }}</h2>
              <p class="profile-type">Usuário Premium</p>
            </div>

            <div class="profile-info">
              <p class="info-item"><strong>Email:</strong> {{ email }}</p>
              <p class="info-item"><strong>Telefone:</strong> {{ phone }}</p>
              <p class="info-item"><strong>Bio:</strong> {{ bio }}</p>
            </div>
          </ion-card-content>
        </ion-card>
      </div>
      
      <ion-modal :is-open="isEditModalOpen">
        <div>
          <ion-header>
            <ion-toolbar color="primary">
              <ion-title class="ion-text-center">Editar Perfil</ion-title>
              <ion-buttons slot="end">
                <ion-button @click="closeEditModal">Fechar</ion-button>
              </ion-buttons>
            </ion-toolbar>
          </ion-header>
          <ion-content style="height: 80vh;">
            <ion-card class="main-card">
              <ion-card-content>
                <ion-item lines="none" class="avatar-item">
                  <ion-avatar slot="start" class="edit-avatar" @click="triggerFileInput">
                    <img :src="profileImage || 'https://placehold.co/100'" alt="Avatar do Usuário" />
                  </ion-avatar>
                  <ion-label>Alterar Imagem</ion-label>
                  <input ref="fileInput" type="file" accept="image/*" style="display:none" @change="onFileSelected">
                </ion-item>

                <ion-item lines="full">
                  <ion-label position="floating">Nome</ion-label>
                  <ion-input v-model="name" class="custom-input" style="margin-top: 5px;"></ion-input>
                </ion-item>

                <ion-item lines="full">
                  <ion-label position="floating">Email</ion-label>
                  <ion-input v-model="email" class="custom-input"></ion-input>
                </ion-item>

                <ion-item lines="full">
                  <ion-label position="floating">Telefone</ion-label>
                  <ion-input v-model="phone" class="custom-input"></ion-input>
                </ion-item>

                <ion-item lines="full">
                  <ion-label position="floating">Bio</ion-label>
                  <ion-textarea v-model="bio" class="custom-input"></ion-textarea>
                </ion-item>

                <ion-button expand="block" class="save-button" @click="saveProfile">
                  Salvar
                </ion-button>
              </ion-card-content>
            </ion-card>
          </ion-content>
        </div>
      </ion-modal>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { inject, ref, onMounted } from 'vue';
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonCard,
  IonCardContent,
  IonAvatar,
  IonButton,
  IonModal,
  IonItem,
  IonLabel,
  IonInput,
  IonTextarea,
  IonButtons
} from '@ionic/vue';
import { Filesystem, Directory, Encoding } from '@capacitor/filesystem';

interface Profile {
  profileImage?: string | null;
  name: string;
  email: string;
  phone: string;
  bio: string;
}

const defaultProfile: Profile = {
  name: 'Nome do Usuário',
  email: 'usuario@example.com',
  phone: '(11) 99999-9999',
  bio: 'Uma breve descrição sobre o usuário que pode incluir interesses e outras informações relevantes.',
  profileImage: null
};

const profile = inject<Profile>('profile', defaultProfile) as Profile;

const profileImage = ref<string | null>(profile?.profileImage ?? defaultProfile.profileImage ?? null);
const fileInput = ref<HTMLInputElement | null>(null);
const name = ref<string>(profile?.name || defaultProfile.name);
const email = ref<string>(profile?.email || defaultProfile.email);
const phone = ref<string>(profile?.phone || defaultProfile.phone);
const bio = ref<string>(profile?.bio || defaultProfile.bio);

const isEditModalOpen = ref(false);

function triggerFileInput() {
  fileInput.value?.click();
}

function onFileSelected(event: Event) {
  const target = event.target as HTMLInputElement;
  if (target.files && target.files[0]) {
    const reader = new FileReader();
    reader.onload = (e) => {
      profileImage.value = e.target?.result as string;
    };
    reader.readAsDataURL(target.files[0]);
  }
}

function openEditModal() {
  isEditModalOpen.value = true;
}

function closeEditModal() {
  isEditModalOpen.value = false;
}

async function loadProfile() {
  try {
    const result = await Filesystem.readFile({
      path: 'profile.json',
      directory: Directory.Data
    });
    
    const fileData = typeof result.data === 'string'
      ? result.data
      : await result.data.text();
      
    const data: Profile = JSON.parse(fileData);
    
    name.value = data.name;
    email.value = data.email;
    phone.value = data.phone;
    bio.value = data.bio;
    profileImage.value = data.profileImage ?? null;
  } catch (err) {
    console.warn('Nenhum perfil salvo encontrado, utilizando valores padrão.', err);
    
    await Filesystem.writeFile({
      path: 'profile.json',
      data: JSON.stringify(defaultProfile),
      directory: Directory.Data,
      encoding: Encoding.UTF8
    });
    
    name.value = defaultProfile.name;
    email.value = defaultProfile.email;
    phone.value = defaultProfile.phone;
    bio.value = defaultProfile.bio;
    profileImage.value = defaultProfile.profileImage ?? null;
  }
}

async function saveProfile() {
  const updatedProfile: Profile = {
    name: name.value,
    email: email.value,
    phone: phone.value,
    bio: bio.value,
    profileImage: profileImage.value
  };

  try {
    await Filesystem.writeFile({
      path: 'profile.json',
      data: JSON.stringify(updatedProfile),
      directory: Directory.Data,
      encoding: Encoding.UTF8
    });
  } catch (err) {
    console.error("Erro ao salvar o perfil:", err);
  }
  closeEditModal();
}

onMounted(() => {
  loadProfile();
});
</script>

<style scoped>
.profile-container {
  max-width: 600px;
  margin: 0 auto;
}

.main-card {
  margin: 16px;
  border-radius: 16px;
}

.profile-header {
  text-align: center;
  margin-bottom: 20px;
}

.profile-avatar {
  margin: 0 auto;
  width: 100px;
  height: 100px;
}

.profile-name {
  color: var(--ion-color-primary);
  font-size: 24px;
  margin: 10px 0 5px;
}

.profile-type {
  color: var(--ion-color-medium);
  font-size: 16px;
  margin: 0;
}

.profile-info {
  text-align: left;
}

.info-item {
  margin: 10px 0;
  font-size: 16px;
}

.edit-avatar {
  width: 100px;
  height: 100px;
  cursor: pointer;
}

.avatar-item {
  margin-bottom: 20px;
}

.save-button {
  margin-top: 20px;
}

.custom-input {
  --padding-start: 3px;
  --padding-top: 25px;
}
</style>
