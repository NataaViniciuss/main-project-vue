<script lang="ts" setup>
import { ref } from 'vue';
import { QCalendarMonth } from '@quasar/quasar-ui-qcalendar';

const ptBRLocale = {
  days: ['Dom', 'Seg', 'Ter', 'Qua', 'Qui', 'Sex', 'Sáb'],
  daysShort: ['D', 'S', 'T', 'Q', 'Q', 'S', 'S'],
  months: [
    'Janeiro',
    'Fevereiro',
    'Março',
    'Abril',
    'Maio',
    'Junho',
    'Julho',
    'Agosto',
    'Setembro',
    'Outubro',
    'Novembro',
    'Dezembro',
  ],
  monthsShort: ['Jan', 'Fev', 'Mar', 'Abr', 'Mai', 'Jun', 'Jul', 'Ago', 'Set', 'Out', 'Nov', 'Dez'],
};

const selectedDate = ref(new Date().toISOString().substring(0, 10));
const date = ref('');
const model = ref('');
const dialog = ref(false);
const nameOfEvent = ref();
const listaDeEventos = ref();

function clearField() {
  nameOfEvent.value = '';
  // Idedit.value = '';
  date.value = '';
  model.value = '';
  dialog.value = false;
}

async function addIdea() {
  // if (Idedit.value) {
  //   await editIdea(Idedit.value);
  //   console.log(Idedit.value);
  //   return;
  // }

  const newEvent = {
    name: nameOfEvent.value,
    date: date.value,
    hours: model.value,
  };
  try {
    const response = await fetch('http://localhost:3000/ListForEvents', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(newEvent),
    });
    if (!response.ok) {
      throw new Error('Erro ao Salvar Evento');
    }

    console.log('Evento salvo com sucesso!');
    await reloadList();
    clearField();
  } catch (error) {
    console.error('Erro ao salvar:', error);
  }
}

async function reloadList() {
  try {
    const response = await fetch('http://localhost:3000/ListForEvents');
    const data = await response.json();
    listaDeEventos.value = data;
  } catch (error) {
    console.error('Erro ao carregar lista de Eventos:', error);
  }
}
</script>

<template>
  <q-btn
    label="Adicionar Evento"
    @click="
      () => {
        const now = new Date();
        date = now.toISOString().substring(0, 10).replace(/-/g, '/');
        model = now.toTimeString().substring(0, 8);
        dialog = true;
      }
    "
  ></q-btn>
  <q-btn label="Excluir Evento"></q-btn>
  <q-dialog v-model="dialog">
    <q-card>
      <q-card-section>
        <div class="row">
          <q-input v-model="nameOfEvent" label="Nome do Evento" class="col-12"></q-input>

          <q-date
            v-model="date"
            class="col-6"
            color="purple"
            style="box-shadow: none; border-radius: 0"
            :locale="ptBRLocale"
          ></q-date>
          <q-time
            v-model="model"
            class="col-6"
            color="purple"
            style="box-shadow: none; border-radius: 0"
            :locale="ptBRLocale"
          ></q-time>
        </div>
      </q-card-section>
      <q-card-action>
        <div>
          <q-btn label="Adicionar" @click="addIdea()"> </q-btn>
          <q-btn label="Cancelar" @click="dialog = false"></q-btn>
        </div>
      </q-card-action>
    </q-card>
  </q-dialog>
  <q-card-section>
    <q-calendar-month
      v-model="selectedDate"
      :weekdays="[0, 1, 2, 3, 4, 5, 6]"
      :day-min-height="140"
      :day-class="getDayClass"
      :locale="ptBRLocale"
    />
  </q-card-section>
</template>
