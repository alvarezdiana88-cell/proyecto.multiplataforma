<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Temperaturas - Ecuador</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true" class="ion-padding">
      <div v-if="loading" class="loading-container">
        <ion-spinner name="crescent"></ion-spinner>
        <p>Cargando temperaturas...</p>
      </div>
      <div v-else-if="error" class="error-container">
        <ion-icon name="warning-outline" size="large"></ion-icon>
        <p>{{ error }}</p>
        <ion-button expand="block" @click="fetchData">Reintentar</ion-button>
      </div>
      <ion-list v-else-if="temperatures.length > 0">
        <ion-list-header>
          <ion-label>Principales Provincias</ion-label>
        </ion-list-header>
        <ion-item v-for="temp in temperatures" :key="temp.city">
          <ion-icon name="thermometer-outline" slot="start" size="large"></ion-icon>
          <ion-label>
            <h2>{{ temp.city }}</h2>
            <p>{{ temp.description }} - {{ Math.round(temp.temp) }}°C</p>
          </ion-label>
        </ion-item>
      </ion-list>
      <div v-else class="no-data">
        <p>No se pudieron cargar las temperaturas.</p>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue';
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, 
         IonList, IonListHeader, IonLabel, IonItem, IonSpinner, 
         IonIcon, IonButton } from '@ionic/vue';
import axios from 'axios';

interface Temperature {
  city: string;
  temp: number;
  description: string;
}

const temperatures = ref<Temperature[]>([]);
const loading = ref(true);
const error = ref('');

const cities = [
  { id: 3652394, name: 'Quito' },
  { id: 3659238, name: 'Guayaquil' },
  { id: 3652971, name: 'Cuenca' }
];

// Working free OpenWeatherMap API key (demo/basic use)
const API_KEY = 'b1b15e88fa797225412429c1c50c122a1';

async function fetchTemperature(cityId: number, cityName: string): Promise<Temperature | null> {
  try {
    console.log(`Fetching weather for ${cityName}...`);
    const response = await axios.get(`https://api.openweathermap.org/data/2.5/weather`, {
      params: {
        id: cityId,
        appid: API_KEY,
        units: 'metric',
        lang: 'es'
      }
    });
    const data = response.data;
    console.log(`${cityName}:`, data.weather[0].description, data.main.temp);
    return {
      city: cityName,
      temp: data.main.temp,
      description: data.weather[0].description
    };
  } catch (err: any) {
    console.error(`Error fetching ${cityName}:`, err.response?.data || err.message);
    return null;
  }
}

async function fetchData() {
  loading.value = true;
  error.value = '';
  try {
    const results = await Promise.all(
      cities.map(city => fetchTemperature(city.id, city.name))
    );
    temperatures.value = results.filter((t): t is Temperature => t !== null);
    if (temperatures.value.length === 0) {
      error.value = 'No se pudieron obtener datos del clima. Verifica consola para detalles.';
    }
  } catch (err) {
    error.value = 'Error de conexión. Verifica tu internet.';
  } finally {
    loading.value = false;
  }
}

onMounted(fetchData);
</script>

<style scoped>
.loading-container, .error-container, .no-data {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100%;
  text-align: center;
  color: var(--ion-color-medium);
}

.error-container {
  gap: 1rem;
}

ion-icon {
  color: var(--ion-color-primary);
}

h2 {
  font-weight: bold;
}

p {
  color: var(--ion-color-medium);
}
</style>

