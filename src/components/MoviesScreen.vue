<script lang="ts" setup>
import { useQuasar } from 'quasar';
import type { Movies } from 'src/interface/Movies';
import { computed, onMounted, ref } from 'vue';

const $q = useQuasar();
const cardWidth = computed(() => ($q.screen.lt.lg ? '80vw' : '18vw'));
const cardHeight = computed(() => ($q.screen.lt.lg ? '55vw' : '12vw'));
const dialog = ref(false);
const nameMovie = ref();
const nameDescription = ref();
const imageUpload = ref<string>(new URL('../assets/icon-movie/upload.png', import.meta.url).href);
const file = ref();
const listaDeFilmes = ref<Movies[]>([]);
const idEdit = ref();
const buttonFile = ref();

function openFile() {
  buttonFile.value.pickFiles();
}

function fileToBase64(file: File) {
  const reader = new FileReader();
  reader.readAsDataURL(file);
  reader.onload = () => {
    imageUpload.value = reader.result as string;
  };
  reader.onerror = (error) => {
    console.error('Erro ao converter imagem para Base64:', error);
  };
}

async function addMovie() {
  if (idEdit.value) {
    await EditMovie(idEdit.value);
    console.log(idEdit.value);
    return;
  }

  const newMovie = {
    nome: nameMovie.value,
    descricao: nameDescription.value,
    imagem: imageUpload.value,
  };
  try {
    const response = await fetch('http://localhost:3000/MoviePngUpload', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(newMovie),
    });

    if (!response.ok) {
      throw new Error('Erro ao adicionar filme');
    }

    console.log('Filme salvo com sucesso!');
    await RealoadMovie();
    clearField();
  } catch (error) {
    console.error('Erro ao Salvar o Filme:', error);
  }
}

function clearField() {
  nameMovie.value = '';
  nameDescription.value = '';
  imageUpload.value = new URL('../assets/icon-movie/upload.png', import.meta.url).href;
  idEdit.value = '';
  dialog.value = false;
}

async function RealoadMovie() {
  try {
    const response = await fetch('http://localhost:3000/MoviePngUpload');
    const data = await response.json();
    listaDeFilmes.value = data;
  } catch (error) {
    console.error('Erro ao carregar lista de Filmes:', error);
  }
}

async function deleteMovie(id: string) {
  try {
    const response = await fetch(`http://localhost:3000/MoviePngUpload/${id}`, {
      method: 'DELETE',
    });

    if (!response.ok) {
      throw new Error('Erro ao Deletar o Filme');
    }
    await RealoadMovie();
    console.log('Filme deletado com Sucesso');
  } catch (erro) {
    console.error(erro);
  }
}

async function EditMovie(id: string) {
  const filmeEditado = {
    nome: nameMovie.value,
    descricao: nameDescription.value,
    imagem: imageUpload.value,
  };
  try {
    const response = await fetch(`http://localhost:3000/MoviePngUpload/${id}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(filmeEditado),
    });

    if (!response.ok) {
      throw new Error('Erro ao editar Filme');
    }
    console.log('Filme editado com sucesso');
    await RealoadMovie();
    clearField();
  } catch (error) {
    console.error('Erro ao editar Filme:', error);
  }
}

function openEditMovie(movie: Movies) {
  nameMovie.value = movie.nome;
  nameDescription.value = movie.descricao;
  imageUpload.value = movie.imagem;
  dialog.value = true;
  idEdit.value = movie.id;
}
onMounted(RealoadMovie);
</script>
<template>
  <q-btn label="Adicionar Filme" @click="dialog = true"></q-btn>
  <q-btn label="Excluir Filme"></q-btn>
  <q-markup-table>
    <thead>
      <tr>
        <th class="text-left">Imagem</th>
        <th class="text-left">Nome do Filme</th>
        <th class="text-left">Anotações</th>
        <th>Editar</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(MoviePngUpload, index) in listaDeFilmes" :key="index">
        <td>
          <q-avatar>
            <img :src="MoviePngUpload.imagem" alt="Imagem do Filme" />
          </q-avatar>
        </td>
        <td>{{ MoviePngUpload.nome }}</td>
        <td>{{ MoviePngUpload.descricao }}</td>
        <td>
          <div class="absolute-center">
            <q-btn label="Editar" @click="openEditMovie(MoviePngUpload)" class="bg-green-7"></q-btn>
            <q-btn label="Excluir" @click="deleteMovie(MoviePngUpload.id)" class="bg-red-7"></q-btn>
          </div>
        </td>
      </tr>
    </tbody>
  </q-markup-table>
  <q-dialog v-model="dialog" persistent>
    <q-card :style="{ width: cardWidth, height: cardHeight }">
      <q-card-section class="q-pa-lg">
        <div class="row q-gutter-md">
          <q-btn round @click="openFile">
            <q-avatar size="400%">
              <q-img v-if="imageUpload" :src="imageUpload" style="max-height: 200px" />
            </q-avatar>
          </q-btn>
          <q-file
            v-model="file"
            accept=".jpg, .png, image/*"
            @update:model-value="fileToBase64"
            label="Adicionar Imagem"
            ref="buttonFile"
            style="display: none"
          ></q-file>
          <q-input v-model="nameMovie" label="Nome do Filme" type="text" class="col-8"></q-input>
          <q-input v-model="nameDescription" label="Descrição" type="text" class="col-11"></q-input>
        </div>
      </q-card-section>

      <q-card-action class="absolute-bottom">
        <div class="q-pa-md">
          <q-btn label="Enviar" @click="addMovie" :disable="!imageUpload"></q-btn>
          <q-btn label="Cancelar" @click="clearField"></q-btn>
        </div>
      </q-card-action>
    </q-card>
  </q-dialog>
</template>
