<script lang="ts" setup>
import { useQuasar } from 'quasar';
import type { Games } from 'src/interface/Games.ts';

import { computed, onMounted, ref } from 'vue';

const $q = useQuasar();
const cardWidth = computed(() => ($q.screen.lt.lg ? '80vw' : '18vw'));
const cardHeight = computed(() => ($q.screen.lt.lg ? '55vw' : '12vw'));
const dialogAdd = ref(false);
const dialogDelete = ref(false);
const select = ref('Gamers');
const input = ref();
const imgGames = ref('../assets/icon-games/Controle.png');
const listaDeJogos = ref<Games[]>([]);
const IdEdit = ref<string | null>(null);

function play(name: string, img: string) {
  select.value = name;
  imgGames.value = img;
}
function clearField() {
  select.value = 'Gamers';
  imgGames.value = '../assets/icon-games/Controle.png';
  dialogAdd.value = false;
  input.value = '';
  IdEdit.value = '';
}

function openEditGame(jogo: Games) {
  select.value = jogo.nome;
  input.value = jogo.descricao;
  imgGames.value = jogo.imagem;
  dialogAdd.value = true;
  IdEdit.value = jogo.id;
}

async function addGame() {
  if (!input.value || select.value === 'Gamers') {
    alert('Escolha um jogo e insira uma descrição.');
    return;
  }

  if (IdEdit.value) {
    await editarJogo(IdEdit.value);
    console.log(IdEdit.value);
    return;
  }
  const newGame = {
    nome: select.value,
    imagem: imgGames.value,
    descricao: input.value,
  };

  try {
    await fetch('https://project-vue-orcin.vercel.app/TabelaDeJogos', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(newGame),
    });

    console.log('jogo salvo com sucesso!');
    await carregarJogos();
    clearField();
  } catch (error) {
    console.error('Erro ao salvar:', error);
  }
}

async function carregarJogos() {
  try {
    const response = await fetch('https://project-vue-orcin.vercel.app/TabelaDeJogos');
    const data = await response.json();
    listaDeJogos.value = data;
  } catch (error) {
    console.error('Erro ao carregar jogos:', error);
  }
}
onMounted(carregarJogos);

async function apagarJogos(id: string) {
  try {
    await fetch(`https://project-vue-orcin.vercel.app/TabelaDeJogos/${id}`, {
      method: 'DELETE',
    });

    await carregarJogos();
    console.log('jogo deletado com sucesso');
  } catch (erro) {
    console.error(erro);
  }
}

async function editarJogo(id: string) {
  const jogoEditado = {
    nome: select.value,
    imagem: imgGames.value,
    descricao: input.value,
  };
  try {
    await fetch(`https://project-vue-orcin.vercel.app/TabelaDeJogos/${id}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(jogoEditado),
    });

    console.log('Jogo editado com sucesso!');
    await carregarJogos();
    clearField();
  } catch (error) {
    console.error('Erro ao editar:', error);
  }
}
</script>
<template>
  <q-btn label="Adicionar jogo" @click="dialogAdd = true"></q-btn>
  <q-btn label="Excluir jogo" @click="dialogDelete = true"></q-btn>

  <q-markup-table>
    <thead>
      <tr>
        <th class="text-left">Imagem</th>
        <th class="text-left">Nome do jogo</th>
        <th class="text-left">Anotações</th>
        <th>Editar</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(TabelaDeJogos, index) in listaDeJogos" :key="index">
        <td>
          <q-avatar>
            <img :src="TabelaDeJogos.imagem" alt="" />
          </q-avatar>
        </td>
        <td>{{ TabelaDeJogos.nome }}</td>
        <td>{{ TabelaDeJogos.descricao }}</td>
        <td>
          <div class="absolute-center">
            <q-btn label="editar" @click="openEditGame(TabelaDeJogos)" class="bg-green-7"></q-btn>
            <q-btn label="excluir" @click="apagarJogos(TabelaDeJogos.id)" class="bg-red-7"></q-btn>
          </div>
        </td>
      </tr>
    </tbody>
  </q-markup-table>
  <q-dialog v-model="dialogAdd" persistent>
    <q-card :style="{ width: cardWidth, height: cardHeight }">
      <q-card-section class="q-pa-lg">
        <div class="row q-gutter-md">
          <q-avatar size="300%">
            <img :src="imgGames" alt="Logo do jogo escolhido" />
          </q-avatar>
          <q-btn-dropdown :label="select" class="col-9">
            <q-list>
              <q-item
                clickable
                v-close-popup
                @click="play('Brawl Stars', '../assets/icon-games/BrawlStars.png')"
              >
                <div class="row q-gutter-sm">
                  <q-avatar size="250%">
                    <img src="../assets/icon-games/BrawlStars.png" alt="Brawl Stars Logo" />
                  </q-avatar>
                  <q-item-section>
                    <q-item-label>Brawl Stars</q-item-label>
                  </q-item-section>
                </div>
              </q-item>
              <q-item
                clickable
                v-close-popup
                @click="play('Minecraft', '../assets/icon-games/Minecraft.png')"
              >
                <div class="row q-gutter-sm">
                  <q-avatar size="250%">
                    <img src="../assets/icon-games/Minecraft.png" alt="Minecraft Logo" />
                  </q-avatar>
                  <q-item-section>
                    <q-item-label>Minecraft</q-item-label>
                  </q-item-section>
                </div>
              </q-item>
              <q-item
                clickable
                v-close-popup
                @click="play('Free Fire', '../assets/icon-games/FreeFire.png')"
              >
                <div class="row q-gutter-sm">
                  <q-avatar size="250%">
                    <img src="../assets/icon-games/FreeFire.png" alt="Free Fire Logo" />
                  </q-avatar>
                  <q-item-section>
                    <q-item-label>Free Fire</q-item-label>
                  </q-item-section>
                </div>
              </q-item>
              <q-item
                clickable
                v-close-popup
                @click="play('Rocket League', '../assets/icon-games/RocketLeague.jpg')"
              >
                <div class="row q-gutter-sm">
                  <q-avatar size="250%">
                    <img src="../assets/icon-games/RocketLeague.jpg" alt="Rocket League Logo" />
                  </q-avatar>
                  <q-item-section>
                    <q-item-label>Rocket League</q-item-label>
                  </q-item-section>
                </div>
              </q-item>
              <q-item
                clickable
                v-close-popup
                @click="play('God Of War 4', '../assets/icon-games/GOW.jpg')"
              >
                <div class="row q-gutter-sm">
                  <q-avatar size="250%">
                    <img src="../assets/icon-games/GOW.jpg" alt="God Of War 4 Logo" />
                  </q-avatar>
                  <q-item-section>
                    <q-item-label>God Of War 4</q-item-label>
                  </q-item-section>
                </div>
              </q-item>
              <q-item
                clickable
                v-close-popup
                @click="play('Red Dead Redemption II', '../assets/icon-games/RDR.png')"
              >
                <div class="row q-gutter-sm">
                  <q-avatar size="250%">
                    <img src="../assets/icon-games/RDR.png" alt="Red Dead Redemption II Logo" />
                  </q-avatar>
                  <q-item-section>
                    <q-item-label>Red Dead Redemption II</q-item-label>
                  </q-item-section>
                </div>
              </q-item>
              <q-item
                clickable
                v-close-popup
                @click="play('Marvel Rivals', '../assets/icon-games/MarvelRivals.jpg')"
              >
                <div class="row q-gutter-sm">
                  <q-avatar size="250%">
                    <img src="../assets/icon-games/MarvelRivals.jpg" alt="Marvel Rivals Logo" />
                  </q-avatar>
                  <q-item-section>
                    <q-item-label>Marvel Rivals</q-item-label>
                  </q-item-section>
                </div>
              </q-item>
              <q-item
                clickable
                v-close-popup
                @click="play('Cuphead', '../assets/icon-games/Cuphead.png')"
              >
                <div class="row q-gutter-sm">
                  <q-avatar size="250%">
                    <img src="../assets/icon-games/Cuphead.png" alt="Cuphead Logo" />
                  </q-avatar>
                  <q-item-section>
                    <q-item-label>Cuphead</q-item-label>
                  </q-item-section>
                </div>
              </q-item>
              <q-item
                clickable
                v-close-popup
                @click="play('Fortnite', '../assets/icon-games/Fortnite.jpg')"
              >
                <div class="row q-gutter-sm">
                  <q-avatar size="250%">
                    <img src="../assets/icon-games/Fortnite.jpg" alt="Fortnite Logo" />
                  </q-avatar>
                  <q-item-section>
                    <q-item-label>Fortnite</q-item-label>
                  </q-item-section>
                </div>
              </q-item>
              <q-item
                clickable
                v-close-popup
                @click="play('Boomerang Fu', '../assets/icon-games/BoomerangFu.jpg')"
              >
                <div class="row q-gutter-sm">
                  <q-avatar size="250%">
                    <img src="../assets/icon-games/BoomerangFu.jpg" alt="Boomerang Fu Logo" />
                  </q-avatar>
                  <q-item-section>
                    <q-item-label>Boomerang Fu</q-item-label>
                  </q-item-section>
                </div>
              </q-item>
              <q-item
                clickable
                v-close-popup
                @click="play('Counter-Strike 2', '../assets/icon-games/cs2.png')"
              >
                <div class="row q-gutter-sm">
                  <q-avatar size="250%">
                    <img src="../assets/icon-games/cs2.png" alt="Counter-Strike 2" />
                  </q-avatar>
                  <q-item-section>
                    <q-item-label>Counter-Strike 2</q-item-label>
                  </q-item-section>
                </div>
              </q-item>
            </q-list>
          </q-btn-dropdown>
          <q-input v-model="input" label="descrição" class="col-11" />
        </div>
      </q-card-section>

      <q-card-actions class="absolute-bottom">
        <div class="q-pa-sm">
          <q-btn label="Adiocionar" @click="addGame"></q-btn>
          <q-btn label="Cancelar" @click="clearField"></q-btn>
        </div>
      </q-card-actions>
    </q-card>
  </q-dialog>
  <q-dialog v-model="dialogDelete">
    <q-card style="width: 20%">
      <q-card-section>
        <div></div>
      </q-card-section>
    </q-card>
  </q-dialog>
</template>
