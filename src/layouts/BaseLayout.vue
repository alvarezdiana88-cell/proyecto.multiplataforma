<template>
    <ion-page>
        <ion-menu content-id="main-content">
            <ion-header>
            <ion-toolbar color="tertiary">
                <ion-title>Menú Principal</ion-title>
            </ion-toolbar>
            </ion-header>
            <ion-content>
                <ion-list>
                    <ion-item>
                        <ion-avatar aria-hidden="true" slot="start">
                            <img alt="" src="https://api.dicebear.com/7.x/avataaars/svg?seed=Diana">
                        </ion-avatar>
                        <ion-label>{{ userStore.userData.usuario }}</ion-label>
                        <ion-button slot="end" fill="solid" size="small" @click="handleLogout">Salir</ion-button>
                    </ion-item>
                    
                    <ion-accordion-group>
                        <ion-accordion value="first">
                            <ion-item slot="header" color="light">
                                <ion-label>Introducción</ion-label>
                            </ion-item>
                            <div slot="content">
                                <ion-list>
                                    <ion-item button @click="currentSection = 'intro1'; closeMenu()">
                                        <ion-label>Bienvenida</ion-label>
                                    </ion-item>
                                    <ion-item button @click="currentSection = 'intro2'; closeMenu()">
                                        <ion-label>Instrucciones</ion-label>
                                    </ion-item>
                                </ion-list>
                            </div>
                        </ion-accordion>
                        
                        <ion-accordion value="second">
                            <ion-item slot="header" color="light">
                                <ion-label>Herramientas</ion-label>
                            </ion-item>
                            <div slot="content">
                                <ion-list>
                                    <ion-item button @click="goToCamera">
                                        <ion-icon slot="start" :icon="cameraOutline"></ion-icon>
                                        <ion-label>Cámara</ion-label>
                                    </ion-item>
                                </ion-list>
                            </div>
                        </ion-accordion>

                        <ion-accordion value="third">
                            <ion-item slot="header" color="light">
                                <ion-label>Acerca de</ion-label>
                            </ion-item>
                            <div slot="content">
                                <ion-list>
                                    <ion-item button @click="currentSection = 'about'; closeMenu()">
                                        <ion-label>Nosotros</ion-label>
                                    </ion-item>
                                    <ion-item button @click="currentSection = 'contact'; closeMenu()">
                                        <ion-label>Contacto</ion-label>
                                    </ion-item>
                                </ion-list>
                            </div>
                        </ion-accordion>
                    </ion-accordion-group>
                </ion-list>
            </ion-content>
        </ion-menu>

        <ion-header>
            <ion-toolbar>
                <ion-buttons slot="start">
                    <ion-menu-button></ion-menu-button>
                </ion-buttons>
                <ion-title>{{ sectionTitle }}</ion-title>
            </ion-toolbar>
        </ion-header>

        <ion-content id="main-content" :class="['ion-padding', sectionClass]">
            <div v-if="currentSection === 'intro1'">
                <h1>Bienvenida</h1>
                <p>Esta es la página de bienvenida con un fondo azul claro.</p>
                <img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Diana" alt="Imagen de bienvenida" style="width: 150px; margin-top: 20px;">
            </div>
            <div v-else-if="currentSection === 'intro2'">
                <h1>Instrucciones</h1>
                <p>Aquí encontrarás las instrucciones con un fondo verde claro.</p>
                    <img src="https://media.giphy.com/media/l0MYt5jPR6QX5pnqM/giphy.gif" alt="Imagen de instrucciones" style="width: 150px; margin-top: 20px;">
            </div>
            <div v-else-if="currentSection === 'about'">
                <h1>Nosotros</h1>
                <p>Información sobre nosotros con un fondo amarillo claro.</p>
                <img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Diana" alt="Imagen de nosotros" style="width: 150px; margin-top: 20px;">
            </div>
            <div v-else-if="currentSection === 'contact'">
                <h1>Contacto</h1>
                <p>Página de contacto con un fondo naranja claro.</p>
                <img src="https://media.giphy.com/media/3o7aD2saalBwwftBIY/giphy.gif" alt="Imagen de contacto" style="width: 150px; margin-top: 20px;">
            </div>
            <div v-else>
                <h1>Selecciona una opción</h1>
                <p>Usa el menú para navegar por las diferentes secciones.</p>
                <img src="https://media.giphy.com/media/26ufdipQqU2lhNA4g/giphy.gif " alt="Imagen de inicio" style="width: 150px; margin-top: 20px;">
            </div>
        </ion-content>
    </ion-page>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import { 
    IonButtons, IonContent, IonHeader, IonMenu, IonMenuButton, 
    IonPage, IonTitle, IonToolbar, IonAccordion, IonAccordionGroup, 
    IonItem, IonLabel, IonList, IonAvatar, IonButton, IonIcon, menuController 
} from '@ionic/vue';
import { cameraOutline } from 'ionicons/icons';
import { useUserStore } from '@/stores/user';
import { useRouter } from 'vue-router';

const userStore = useUserStore();
const router = useRouter();
const currentSection = ref('default');

const sectionTitle = computed(() => {
    switch (currentSection.value) {
        case 'intro1': return 'Bienvenida';
        case 'intro2': return 'Instrucciones';
        case 'about': return 'Nosotros';
        case 'contact': return 'Contacto';
        default: return 'Inicio';
    }
});

const sectionClass = computed(() => {
    return `bg-${currentSection.value}`;
});

async function handleLogout() {
    await userStore.$setLogin(null);
    router.push('/login');
}

function goToCamera() {
    closeMenu();
    router.push('/camara');
}

function closeMenu() {
    menuController.close();
}
</script>

<style scoped>
ion-menu::part(backdrop) {
    background-color: rgba(255, 0, 255, 0.5);
}

ion-menu::part(container) {
    border-radius: 0 20px 20px 0;
    box-shadow: 4px 0px 16px rgba(255, 0, 255, 0.18);
}

/* Colores de fondo para cada sección */
.bg-intro1 {
    --background: #e3f2fd; /* Azul claro */
}
.bg-intro2 {
    --background: #e8f5e9; /* Verde claro */
}
.bg-about {
    --background: #fffde7; /* Amarillo claro */
}
.bg-contact {
    --background: #fff3e0; /* Naranja claro */
}
.bg-default {
    --background: #ffffff;
}

h1 {
    color: #333;
}
</style>
