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
</script>
<template>
  <div class="q-gutter-y-md">
    <q-card>
      <q-tabs v-model="tab" class="bg-purple text-dark">
        <q-tab name="gamers" id="gamers">
          <q-badge color="primary" text-color="white" floating>2</q-badge>
          <img
            src="../assets/icons-header/controle.png"
            alt="Jogos-logo"
            style="width: 3vh; height: 3vh"
          />
          <label for="gamers">Gamers</label>
        </q-tab>
        <q-tab name="movies" id="movies">
          <q-badge color="primary" text-color="white" floating>2</q-badge>
          <img
            src="../assets/icons-header/netflix.png"
            alt="Filmes-logo"
            style="width: 3vh; height: 3vh"
          />
          <label for="movies">Movies</label>
        </q-tab>
        <q-tab name="marriage" id="marriage">
          <q-badge color="primary" text-color="white" floating>2</q-badge>
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
          <GamersScreen />
        </q-tab-panel>
        <q-tab-panel name="movies">
          <MoviesScreen />
        </q-tab-panel>
        <q-tab-panel name="marriage">
          <MarriageScreen />
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
