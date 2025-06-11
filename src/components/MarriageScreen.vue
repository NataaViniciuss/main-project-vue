<script lang="ts" setup>
import { useQuasar } from 'quasar';
import type { Ideas } from 'src/interface/Ideas';
import { computed, onMounted, ref } from 'vue';

const $q = useQuasar();
const cardWidth = computed(() => ($q.screen.lt.lg ? '80vw' : '18vw'));
const cardHeight = computed(() => ($q.screen.lt.lg ? '55vw' : '12vw'));
const dialog = ref(false);
const nameOfIdea = ref();
const linkOfIdea = ref();
const nameSelect = ref('Rede Sociais');
const imgIdea = ref();
const urlTTK = ref();
const urlInsta = ref();
const vImgIdea = computed(() => !!imgIdea.value);
const listaDeIdeias = ref<Ideas[]>([]);
const Idedit = ref<string | null>(null);
const primeiraEtapa = ref();

function openEditIdea(idea: Ideas) {
  nameOfIdea.value = idea.name;
  linkOfIdea.value = idea.link;
  imgIdea.value = idea.image;
  nameSelect.value = idea.select;
  dialog.value = true;
  Idedit.value = idea.id;
}

async function CheckSocialNetwork() {
  if (nameSelect.value === 'Youtube') {
    linkOfIdea.value = linkOfIdea.value.includes('https://youtube.com/shorts/')
      ? linkOfIdea.value.replace('https://youtube.com/shorts/', 'https://www.youtube.com/embed/')
      : linkOfIdea.value.replace('https://youtu.be/', 'https://www.youtube.com/embed/');

    await addIdea();
  }
  if (nameSelect.value === 'TikTok') {
    urlTTK.value = linkOfIdea.value.includes('?')
      ? linkOfIdea.value.substring(0, linkOfIdea.value.indexOf('?'))
      : linkOfIdea;

    linkOfIdea.value = 'https://www.tiktok.com/embed/' + urlTTK.value.split('/video/')[1];

    await addIdea();
  }
  if (nameSelect.value === 'Instagram') {
    urlInsta.value = linkOfIdea.value.includes('?')
      ? linkOfIdea.value.substring(0, linkOfIdea.value.indexOf('?'))
      : linkOfIdea;
    linkOfIdea.value = urlInsta.value + 'embed';
    await addIdea();
  }
}

function redeSociais(nome: string, img: string) {
  imgIdea.value = new URL(img, import.meta.url).href;
  nameSelect.value = nome;
}

function clearField() {
  nameSelect.value = 'Rede Sociais';
  imgIdea.value = '';
  dialog.value = false;
  linkOfIdea.value = '';
  nameOfIdea.value = '';
  urlTTK.value = '';
  urlInsta.value = '';
  Idedit.value = '';
  primeiraEtapa.value = '';
}

async function addIdea() {
  if (Idedit.value) {
    await editIdea(Idedit.value);
    console.log(Idedit.value);
    return;
  }

  const newIdea = {
    name: nameOfIdea.value,
    link: linkOfIdea.value,
    image: imgIdea.value,
    select: nameSelect.value,
  };
  try {
    await fetch('https://project-vue-orcin.vercel.app/ListaForMarriage', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(newIdea),
    });

    console.log('Ideia salva com sucesso!');
    await reloadList();
    clearField();
  } catch (error) {
    console.error('Erro ao salvar:', error);
  }
}

async function reloadList() {
  try {
    const response = await fetch('https://project-vue-orcin.vercel.app/ListaForMarriage');
    const data = await response.json();
    listaDeIdeias.value = data;
  } catch (error) {
    console.error('Erro ao carregar lista de Ideias:', error);
  }
}

async function editIdea(id: string) {
  const IdeiaEditada = {
    name: nameOfIdea.value,
    link: linkOfIdea.value,
    image: imgIdea.value,
    select: nameSelect.value,
  };

  try {
    await fetch(`https://project-vue-orcin.vercel.app/ListaForMarriage/${id}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(IdeiaEditada),
    });

    console.log('Ideia editada com sucesso!');
    await reloadList();
    clearField();
  } catch (error) {
    console.error('Erro ao editar:', error);
  }
}

async function deleteMovie(id: string) {
  try {
    await fetch(`https://project-vue-orcin.vercel.app/ListaForMarriage/${id}`, {
      method: 'DELETE',
    });
    await reloadList();
    console.log('Ideia deletada com sucesso');
  } catch (erro) {
    console.error('Erro ao deletar ideia:', erro);
  }
}

onMounted(reloadList);
</script>
<template>
  <q-btn label="Adicionar Ideia" @click="dialog = true"></q-btn>
  <q-btn label="Excluir Ideia"></q-btn>
  <q-dialog v-model="dialog" persistent>
    <q-card :style="{ width: cardWidth, heigth: cardHeight }">
      <q-card-section class="q-pa-lg">
        <div class="row q-gutter-md">
          <q-input v-model="nameOfIdea" label="Nome da Ideia" class="col-11"></q-input>
          <q-input v-model="linkOfIdea" label="Link da Ideia" class="col-11"></q-input>
          <q-avatar square v-if="vImgIdea" size="300%">
            <img :src="imgIdea" alt="Logo da Rede Social" />
          </q-avatar>
          <q-btn-dropdown :label="nameSelect" :class="vImgIdea ? 'col-9' : 'col-11'">
            <q-list>
              <q-item
                clickable
                v-close-popup
                @click="redeSociais('Instagram', '../assets/instagram-VUFQ3l_n.png')"
              >
                <div class="row q-gutter-sm">
                  <q-avatar square size="250%">
                    <img src="../assets/icon-redes-sociais/instagram.png" alt="Logo Instagram" />
                  </q-avatar>
                  <q-item-section>
                    <q-item-label>Instagram</q-item-label>
                  </q-item-section>
                </div>
              </q-item>
              <q-item
                clickable
                v-close-popup
                @click="redeSociais('Youtube', '../assets/youtube-B2-ob9Jb.png')"
              >
                <div class="row q-gutter-sm">
                  <q-avatar square size="250%">
                    <img src="../assets/icon-redes-sociais/youtube.png" alt="Logo Youtube" />
                  </q-avatar>
                  <q-item-section>
                    <q-item-label> Youtube </q-item-label>
                  </q-item-section>
                </div>
              </q-item>
              <q-item
                clickable
                v-close-popup
                @click="redeSociais('TikTok', '../assets/tiktok-CpNpErwL.png')"
              >
                <div class="row q-gutter-sm">
                  <q-avatar square size="250%">
                    <img src="../assets/icon-redes-sociais/tiktok.png" alt="Logo TikTok" />
                  </q-avatar>
                  <q-item-section>
                    <q-item-label> TikTok </q-item-label>
                  </q-item-section>
                </div>
              </q-item>
              <q-item
                clickable
                v-close-popup
                @click="redeSociais('Pinterest', '../assets/pinterest-RizHWLUV.png')"
              >
                <div class="row q-gutter-sm">
                  <q-avatar square size="250%">
                    <img src="../assets/icon-redes-sociais/pinterest.png" alt="Logo Pinterest" />
                  </q-avatar>
                  <q-item-section>
                    <q-item-label> Pinterest </q-item-label>
                  </q-item-section>
                </div>
              </q-item>
            </q-list>
          </q-btn-dropdown>
        </div>
      </q-card-section>
      <q-card-action>
        <q-btn label="adicionar" @click="CheckSocialNetwork()"></q-btn>
        <q-btn label="cancelar" @click="clearField()"></q-btn>
      </q-card-action>
    </q-card>
  </q-dialog>
  <div class="column" style="width: 100vw">
    <div class="q-py-lg" v-for="(item, index) in listaDeIdeias" :key="index" style="margin: 0px -1vw 0px -1vw" >
      <q-card>
        <q-item>
          <q-item-section avatar>
            <q-avatar square>
              <img :src="item.image" />
            </q-avatar>
          </q-item-section>

          <q-item-section>
            <q-item-label>
              {{ item.name }}
            </q-item-label>
          </q-item-section>
        </q-item>

        <q-video :src="item.link" style="width: 100vw; height: 82vh"></q-video>
        <!-- <div ref="tiktokEmbedContainer">
            <blockquote
              class="tiktok-embed"
              :cite="item.urlTTK"
              :data-video-id="item.idVideoTTK"
              style="max-width: 605px; min-width: 325px"
            >
              <section></section>
            </blockquote>
          </div> -->

        <q-card-actions align="right" class="q-gutter-md">
          <q-btn class="bg-green-7" label="Editar" @click="openEditIdea(item)"></q-btn>
          <q-btn class="bg-red-7" label="Excluir" @click="deleteMovie(item.id)"></q-btn>
        </q-card-actions>
      </q-card>
    </div>
  </div>
</template>
