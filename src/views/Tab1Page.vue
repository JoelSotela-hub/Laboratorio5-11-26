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

        <ion-button expand="block" class="ion-margin-top" @click="enviar">Enviar</ion-button>

        <ion-button expand="block" class="ion-margin-top" @click="actualizar">Actualizar</ion-button>
      </ion-list>

      

      <ExploreContainer name="Formulário de Registro" />
    </ion-content>

  </ion-page>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, 
  IonList, IonItem, IonInput, IonButton } from '@ionic/vue';
import ExploreContainer from '@/components/ExploreContainer.vue';

const nombre = ref('');
const body = ref('');
const cargando = ref(false);

const actualizar = async () => {
  try {
    const respuesta = await fetch('https://jsonplaceholder.typicode.com/posts/1', {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json; charset=UTF-8' },
      body: JSON.stringify({
        id: 1,
        title: nombre.value,
        body: body.value,
        userId: 1,
      }),
});
  if (!respuesta.ok) throw new Error(`Error ${respuesta.status}`);

    const datos = await respuesta.json();
    console.log('Actualizado:', datos);
  } catch (error) {
    console.error(error);
  }
};

const enviar = () => {
  console.log('Nombre:', nombre.value);
  console.log('Body:', body.value);
};

</script>
