<script lang="ts" setup>
import { ref } from 'vue';

const now = new Date().toISOString().substring(0, 10);
const selectedDate = ref(now); // <-- Corrigido aqui

const locale = 'pt-BR';

const events = ref([
  {
    title: 'Reunião com equipe',
    start: '2025-05-16 09:00',
    end: '2025-05-16 10:00',
    color: 'blue',
  },
  {
    title: 'Almoço',
    start: '2025-05-16 12:00',
    end: '2025-05-16 13:00',
    color: 'green',
  },
]);
</script>

<template>
  <q-card-section style="height: 800px">
    <!-- altura aumentada -->
    <q-calendar-week
      v-model="selectedDate"
      :weekdays="[0, 1, 2, 3, 4, 5, 6]"
      :interval-start="8"
      :interval-count="10"
      :interval-minutes="60"
      :interval-height="50"
      :events="events"
      :event-start="(event: any) => event.start"
      :event-end="(event: any) => event.end"
      :now="now"
      :locale="locale"
    />
  </q-card-section>
</template>
