<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Tab 1</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true" class="ion-padding">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Registro de Usuario</ion-title>
        </ion-toolbar>
      </ion-header>

      <ion-list>
        <ion-item>
          <ion-input v-model="nombre" label="Nombre" label-placement="floating"></ion-input>
        </ion-item>

        <ion-item>
          <ion-input v-model="body" label="Body" label-placement="floating"></ion-input>
        </ion-item>
      </ion-list>

      <ion-button expand="block" fill="outline" class="ion-margin-top" @click="escanear">
        Escanear código
      </ion-button>

      <ion-button expand="block" class="ion-margin-top" :disabled="cargando" @click="enviar">
        {{ cargando ? 'Enviando...' : 'Enviar' }}
      </ion-button>

      <ExploreContainer name="Formulário de Registro" />
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import {
  IonPage, IonHeader, IonToolbar, IonTitle, IonContent,
  IonList, IonItem, IonInput, IonButton, toastController,
} from '@ionic/vue';
import { Capacitor } from '@capacitor/core';
import {
  CapacitorBarcodeScanner,
  CapacitorBarcodeScannerTypeHint,
} from '@capacitor/barcode-scanner';
import ExploreContainer from '@/components/ExploreContainer.vue';

const nombre = ref('');
const body = ref('');
const cargando = ref(false);

const mostrarMensaje = async (message: string, color: string) => {
  const toast = await toastController.create({ message, duration: 2000, color });
  await toast.present();
};

// Escanea un código y lo coloca en el campo "Body"
const escanear = async () => {
  if (!Capacitor.isNativePlatform()) {
    await mostrarMensaje('El escáner solo funciona en Android o iOS', 'warning');
    return;
  }

  try {
    const lectura = await CapacitorBarcodeScanner.scanBarcode({
      hint: CapacitorBarcodeScannerTypeHint.ALL,
    });
    body.value = lectura.ScanResult;
  } catch (error) {
    console.error('Error al escanear:', error);
    await mostrarMensaje('No se pudo escanear', 'danger');
  }
};

// Envía los datos con POST
const enviar = async () => {
  if (!nombre.value.trim() || !body.value.trim()) {
    await mostrarMensaje('Completa todos los campos', 'warning');
    return;
  }

  cargando.value = true;
  try {
    const respuesta = await fetch('https://jsonplaceholder.typicode.com/posts', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json; charset=UTF-8' },
      body: JSON.stringify({
        title: nombre.value,
        body: body.value,
        userId: 1,
      }),
    });

    if (!respuesta.ok) throw new Error(`Error ${respuesta.status}`);

    const datos = await respuesta.json();
    console.log('Respuesta del servidor:', datos);

    await mostrarMensaje('Enviado correctamente', 'success');
    nombre.value = '';
    body.value = '';
  } catch (error) {
    console.error(error);
    await mostrarMensaje('No se pudo enviar', 'danger');
  } finally {
    cargando.value = false;
  }
};
</script>