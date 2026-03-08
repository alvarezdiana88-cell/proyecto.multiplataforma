<template>
<ion-page>
  <ion-header>
    <ion-toolbar color="primary">
      <ion-buttons slot="start">
        <ion-back-button default-href="/seccion"></ion-back-button>
      </ion-buttons>
      <ion-title>Cámara</ion-title>
    </ion-toolbar>
  </ion-header>
// El contenido de la página con un fondo púrpura suave
  <ion-content class="ion-padding camera-bg">
    <div class="camera-container">
      <h1>Captura una Foto</h1>
      <p>Esta página tiene un fondo de color púrpura suave.</p>
      <ion-button expand="block" color="secondary" @click="takePicture">
        <ion-icon slot="start" :icon="cameraOutline"></ion-icon>
        Tomar Foto
      </ion-button>
      
      <div v-if="imageSrc" class="image-preview">
        <img :src="imageSrc" alt="Captured Image" />
      </div>
      
      <div v-if="imageInfo" class="info-box">
        <p><strong>Ruta de la imagen:</strong></p>
        <code>{{ imageInfo }}</code>
      </div>
    </div>
  </ion-content>
</ion-page>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { 
  IonPage, IonHeader, IonToolbar, IonTitle, IonContent, 
  IonButton, IonButtons, IonBackButton, IonIcon 
} from '@ionic/vue';
import { cameraOutline } from 'ionicons/icons';
import { Camera, CameraResultType } from '@capacitor/camera';

const imageSrc = ref<string | undefined>('');
const imageInfo = ref<string | undefined>('');

const takePicture = async () => {
  try {
    const image = await Camera.getPhoto({
      quality: 90,
      allowEditing: true,
      resultType: CameraResultType.Uri
    });
    const imageUrl = image.webPath;
    imageInfo.value = image.webPath!;
    imageSrc.value = imageUrl;
  } catch (error) {
    console.error('Error al tomar la foto:', error);
  }
};
</script>

<style scoped>
.camera-bg {
  --background: #f3e5f5; /* Púrpura muy claro */
}

.camera-container {
  text-align: center;
  margin-top: 20px;
}

.image-preview {
  margin-top: 20px;
  border: 2px solid #ce93d8;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}

.image-preview img {
  width: 100%;
  display: block;
}

.info-box {
  margin-top: 20px;
  padding: 10px;
  background: #fff;
  border-radius: 5px;
  font-size: 0.8rem;
  text-align: left;
  word-break: break-all;
}

h1 {
  color: #4a148c;
}
</style>