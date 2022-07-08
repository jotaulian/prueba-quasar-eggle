<template>
  <q-page padding>
    <!-- Sección donde se muestra la selección de la plataforma de juegos -->
    <!-- Nota: en html5 <section></section> es equivalente a un <div> (https://developer.mozilla.org/es/docs/Web/HTML/Element/section) -->
    <section class="q-pa-md cabecera">
      <p class="text-h6">Selecciona una plataforma para mostrar los juegos gratuitos disponibles:</p>
      <!-- Componente predefinido en Quasar que muestra las propiedades establecidas en la variable "platforms". El valor seleccionado se guarda en la variable "platform" -->
      <q-select outlined v-model="platform" :options="platforms" option-value="id" option-label="desc">
        <template v-slot:prepend>
          <q-icon name="search" />
        </template>
      </q-select>
    </section>

    <!-- Sección que muestra el número de juegos encontrados -->
    <section>
      <p v-if="data.length > 0" class="q-pt-xs text-center">{{ data.length}} juegos encontrados.</p>
      <p v-else class="q-pt-xs text-center"><em>Selecciona una plataforma para buscar los juegos</em></p>
    </section>

    <!-- Sección donde se carga el listado de juegos disponibles -->
    <section class=" q-mt-xl ">

      <!-- Nota 1: <article></article> es equivalente a un <div> https://developer.mozilla.org/es/docs/Web/HTML/Element/article -->
      <!-- Nota 2: Bucle FOR directamente en HTML con Vue.js (https://es.vuejs.org/v2/guide/list.html) -->
      <article v-for=" item in data" :key="item.id" class="item">
        <!-- Ejemplo del contenido de un item que devuelve el API:<template>
        {
          "id": 521,
          "title": "Diablo Immortal",
          "thumbnail": "https://www.freetogame.com/g/521/thumbnail.jpg",
          "short_description": "Built for mobile and also released on PC, Diablo Immortal fills in the gaps between Diablo II and III in an MMOARPG environment.",
          "game_url": "https://www.freetogame.com/open/diablo-immortal",
          "genre": "MMOARPG",
          "platform": "PC (Windows)",
          "publisher": "Blizzard Entertainment",
          "developer": "Blizzard Entertainment",
          "release_date": "2022-06-02",
          "freetogame_profile_url": "https://www.freetogame.com/diablo-immortal"
        }
      -->
        <!-- Título del juego - En Vue.js se utiliza {{ nombre_variable }} para mostarr un valor en la página -->
        <p class="text-h6"><b>{{ item.title}}</b></p>
        <!-- Foto del juego - En Vue.js se utiliza :atributo, en este caso el atributo es 'src' por lo que se utiliza
        :src="nombre_variable" para que el atributo tome el valor de forma dinámica a partir de la variable -->
        <img :src="item.thumbnail" />
        <!-- Descripción corta del juego -->
        <p>{{ item.short_description}}</p>
        <!-- Información adicional del juego -->
        <ul>

          <li>{{ item.genre}}</li>
          <li>{{ item.platform}}</li>
        </ul>

      </article>
      <q-inner-loading :showing="loading">
        <q-spinner-gears size="50px" color="primary" />
      </q-inner-loading>
    </section>

  </q-page>
</template>

<script>
import {defineComponent, ref, watch} from 'vue'
import {api} from 'boot/axios'
import {useQuasar} from 'quasar'
export default defineComponent({
  name: 'IndexPage',
  setup() {
    const $q = useQuasar()
    const platform = ref(null);
    const data = ref([]);
    const loading = ref(false);

    const loadData = () => {
      loading.value = true
      const params = new URLSearchParams([['platform', platform.value.id], ['sort-by', 'alphabetical']]);
      api.get('/api/games', {
        params, headers: {
          'X-RapidAPI-Key': '67d9436ce8msh5a7f6cb557501a3p1efcadjsn7e754104fc3a',
          'X-RapidAPI-Host': 'free-to-play-games-database.p.rapidapi.com'
        }
      })
        .then((response) => {
          data.value = response.data
          loading.value = false
        })
        .catch(() => {
          $q.notify({
            color: 'negative',
            position: 'top',
            message: 'Error al recuperar datos',
            icon: 'report_problem'
          })
          loading.value = false
        })

    }
    watch(platform, (newvalue, oldvalue) => {
      //console.log('filter by ', newvalue)

      loadData()

    })
    return {
      data,
      platform,
      platforms: [{id: 'all', desc: "Mostrar todos"}, {id: 'pc', desc: "Ver juegos para PC"}, {id: 'browser', desc: 'Ver juegos para Navegador web'}],
      loading
    }
  }
})
</script>
<style >
.cabecera {
  border: 1px solid #dedede;
}

.item {
  margin: 20px;
  border: 1px solid #dedede;
}
</style>
