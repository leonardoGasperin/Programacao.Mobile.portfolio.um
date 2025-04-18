<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Perfil do Usuário</ion-title>
        <ion-buttons slot="end">
          <ion-button @click="openEditModal">Editar Perfil</ion-button>
        </ion-buttons>
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
              <img :src="profileImage || 'https://placehold.co/100'" alt="Avatar do Usuário" />
            </ion-avatar>
            <ion-card-title style="margin-top: 10px;">{{ name }}</ion-card-title>
            <ion-card-subtitle>Usuário Premium</ion-card-subtitle>
          </ion-card-header>
          <ion-card-content>
            <p><strong>Email:</strong> {{ email }}</p>
            <p><strong>Telefone:</strong> {{ phone }}</p>
            <p><strong>Bio:</strong> {{ bio }}</p>
          </ion-card-content>
        </ion-card>
      </div>
      
      <ion-modal :is-open="isEditModalOpen">
        <ng-template>
          <ion-header>
            <ion-toolbar>
              <ion-title>Editar Perfil</ion-title>
              <ion-buttons slot="end">
                <ion-button @click="closeEditModal">Fechar</ion-button>
              </ion-buttons>
            </ion-toolbar>
          </ion-header>
          <ion-content style="height: 80vh;">
            <div style="padding: 20px;">
              <ion-item lines="none">
                <ion-avatar slot="start" style="width: 100px; height: 100px; cursor:pointer" @click="triggerFileInput">
                  <img :src="profileImage || 'https://placehold.co/100'" alt="Avatar do Usuário" />
                </ion-avatar>
                <ion-label>Alterar Imagem</ion-label>
                <input ref="fileInput" type="file" accept="image/*" style="display:none" @change="onFileSelected">
              </ion-item>
              <ion-item>
                <ion-label position="stacked">Nome</ion-label>
                <ion-input v-model="name"></ion-input>
              </ion-item>
              <ion-item>
                <ion-label position="stacked">Email</ion-label>
                <ion-input v-model="email"></ion-input>
              </ion-item>
              <ion-item>
                <ion-label position="stacked">Telefone</ion-label>
                <ion-input v-model="phone"></ion-input>
              </ion-item>
              <ion-item>
                <ion-label position="stacked">Bio</ion-label>
                <ion-textarea v-model="bio"></ion-textarea>
              </ion-item>
              <ion-button expand="block" style="margin-top: 20px;" @click="saveProfile">
                Salvar
              </ion-button>
            </div>
          </ion-content>
        </ng-template>
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
  IonCardHeader,
  IonCardTitle,
  IonCardSubtitle,
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

// Valores padrão do perfil
const defaultProfile: Profile = {
  name: 'Nome do Usuário',
  email: 'usuario@example.com',
  phone: '(11) 99999-9999',
  bio: 'Uma breve descrição sobre o usuário que pode incluir interesses e outras informações relevantes.',
  profileImage: null
};

// Injeta o objeto de perfil (certifique-se de que o provider esteja configurado no pai)
const profile = inject<Profile>('profile', defaultProfile) as Profile;

// Variáveis reativas do perfil
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
    
    // Cria o arquivo com os valores padrão
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
