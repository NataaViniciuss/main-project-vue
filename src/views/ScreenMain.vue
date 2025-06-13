<script lang="ts" setup>
// import { library } from '@fortawesome/fontawesome-svg-core';
// import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
// import { faGamepad } from '@fortawesome/free-solid-svg-icons';
// import '@quasar/extras/fontawesome-v6'; // Ativa Font Awesome no Quasar
// import { faTwitter } from '@fortawesome/free-brands-svg-icons';

// library.add(faGamepad);

import { ref } from 'vue';
import GamersScreen from 'src/components/GamersScreen.vue';
import MoviesScreen from 'src/components/MoviesScreen.vue';
import MarriageScreen from 'src/components/MarriageScreen.vue';
import EventsScreen from 'src/components/EventsScreen.vue';
import RoutineScreen from 'src/components/RoutineScreen.vue';

const tab = ref();
const listaDeIdeias = ref([]);
const listaDeFilmes = ref([]);
const listaDeJogos = ref([]);

async function getListOfIdeas() {
  try {
    const response = await fetch('https://project-vue-orcin.vercel.app/ListaForMarriage', {
      method: 'GET',
    });
    const data = await response.json();
    listaDeIdeias.value = data;
  } catch (error) {
    console.error('Erro ao exibir número de Ideias:', error);
  }
}

async function getListOfMovies() {
  try {
    const response = await fetch('https://project-vue-orcin.vercel.app/MoviePngUpload', {
      method: 'GET',
    });
    const data = await response.json();
    listaDeFilmes.value = data;
  } catch (error) {
    console.error('Erro ao exibir número de Ideias:', error);
  }
}

async function getListOfGames() {
  try {
    const response = await fetch('https://project-vue-orcin.vercel.app/TabelaDeJogos', {
      method: 'GET',
    });
    const data = await response.json();
    listaDeJogos.value = data;
  } catch (error) {
    console.error('Erro ao exibir número de Ideias:', error);
  }
}
</script>
<template>
  <div class="q-gutter-y-md">
    <q-card>
      <q-tabs v-model="tab" class="bg-purple text-dark">
        <q-tab name="gamers" id="gamers">
          <q-badge color="primary" text-color="white" v-if="listaDeJogos.length > 0" floating>{{
            listaDeJogos.length
          }}</q-badge>
          <img
            src="../assets/icons-header/controle.png"
            alt="Jogos-logo"
            style="width: 3vh; height: 3vh"
          />
          <label for="gamers">Gamers</label>
        </q-tab>
        <q-tab name="movies" id="movies">
          <q-badge color="primary" text-color="white" v-if="listaDeFilmes.length > 0" floating>{{
            listaDeFilmes.length
          }}</q-badge>
          <img
            src="../assets/icons-header/netflix.png"
            alt="Filmes-logo"
            style="width: 3vh; height: 3vh"
          />
          <label for="movies">Movies</label>
        </q-tab>
        <q-tab name="marriage" id="marriage">
          <q-badge color="primary" text-color="white" floating v-if="listaDeIdeias.length > 0">
            {{ listaDeIdeias.length }}</q-badge
          >
          <img
            src="../assets/icons-header/casamento.png"
            alt="Casamento-logo"
            style="width: 3vh; height: 3vh"
          />
          <label for="marriage">Marriage</label>
        </q-tab>
        <q-tab name="events" id="events">
          <q-badge color="primary" text-color="white" floating>2</q-badge>
          <img
            src="../assets/icons-header/aliancas-de-casamento.png"
            alt="Eventos-logo"
            style="width: 3vh; height: 3vh"
          />
          <label for="events">Events</label>
        </q-tab>
        <q-tab name="routine" id="routine">
          <q-badge color="primary" text-color="white" floating>2</q-badge>
          <img
            src="../assets/icons-header/haltere.png"
            alt="Rotina-logo"
            style="width: 3vh; height: 3vh"
          />
          <label for="routine">Routine</label>
        </q-tab>
      </q-tabs>
      <q-separator />
      <q-tab-panels v-model="tab">
        <q-tab-panel name="gamers">
          <GamersScreen @on-success="getListOfGames()" />
        </q-tab-panel>
        <q-tab-panel name="movies">
          <MoviesScreen @on-success="getListOfMovies()" />
        </q-tab-panel>
        <q-tab-panel name="marriage">
          <MarriageScreen @on-success="getListOfIdeas()" />
        </q-tab-panel>
        <q-tab-panel name="events">
          <EventsScreen />
        </q-tab-panel>
        <q-tab-panel name="routine">
          <RoutineScreen />
        </q-tab-panel>
      </q-tab-panels>
    </q-card>
  </div>
</template>
