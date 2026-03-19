<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Temperaturas Ecuador</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true" class="ion-padding">
      <div v-if="loading" class="loading-container">
        <ion-spinner></ion-spinner>
        <p>Cargando...</p>
      </div>
      <ion-list v-else>
        <ion-list-header>
          <ion-label>Provincias Principales</ion-label>
        </ion-list-header>
        <ion-item v-for="item in cityStatus" :key="item.city">
          <ion-icon :name="item.icon" slot="start"></ion-icon>
          <ion-label>
            <h2>{{ item.city }}</h2>
            <p v-if="item.temp !== null">{{ item.description }} - {{ Math.round(item.temp) }}°C</p>
            <p v-else class="fallback">{{ item.description }}</p>
            <small v-if="item.error">API falló (fallback)</small>
          </ion-label>
        </ion-item>
      </ion-list>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue';
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonList, IonListHeader, IonLabel, IonItem, IonSpinner, IonIcon } from '@ionic/vue';

interface CityStatus {
  city: string;
  temp: number | null;
  description: string;
  error: string | null;
  icon: string;
}

const cityStatus = ref<CityStatus[]>([]);
const loading = ref(true);

const cities = [
  { name: 'Quito', fallbackTemp: 15, fallbackDesc: 'Nublado', icon: 'partly-sunny-outline' },
  { name: 'Guayaquil', fallbackTemp: 28, fallbackDesc: 'Caluroso', icon: 'sunny-outline' },
  { name: 'Cuenca', fallbackTemp: 18, fallbackDesc: 'Templado', icon: 'cloudy-night-outline' }
];

async function fetchTemp(cityName: string): Promise<{temp: number | null, desc: string, error: string | null}> {
  try {
    const response = await fetch(`https://wttr.in/${cityName}?format=j1`);
    const text = await response.text();
    const jsonData = JSON.parse(text);
    const data = jsonData.data.current_condition[0];
    return { temp: parseFloat(data.temp_C), desc: jsonData.data.weatherDesc[0].value, error: null };
  } catch (err) {
    console.error(cityName, err);
    return { temp: null, desc: '', error: err instanceof Error ? err.message : 'Desconocido' };
  }
}

async function loadData() {
  const promises = cities.map(city => fetchTemp(city.name));
  const results = await Promise.all(promises);
  cityStatus.value = cities.map((city, i) => ({
    city: city.name,
    temp: results[i].temp ?? city.fallbackTemp,
    description: results[i].temp !== null ? results[i].desc : city.fallbackDesc,
    error: results[i].error,
    icon: city.icon
  }));
  loading.value = false;
}

onMounted(loadData);
</script>

<style scoped>
.loading-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 200px;
  justify-content: center;
}
.fallback {
  color: var(--ion-color-medium);
}
small {
  color: var(--ion-color-danger);
}
</style>

