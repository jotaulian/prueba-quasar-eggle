<template>
  <q-page padding class="fondo">
    <!-- Sección donde se muestra la selección de la plataforma de juegos -->
    <!-- Nota: en html5 <section></section> es equivalente a un <div> (https://developer.mozilla.org/es/docs/Web/HTML/Element/section) -->
    <section class="q-pa-md section-style">
      <p class="text-h6">
        Selecciona una plataforma para mostrar los juegos gratuitos disponibles:
      </p>
      <!-- Componente predefinido en Quasar que muestra las propiedades establecidas en la variable "platforms". El valor seleccionado se guarda en la variable "platform" -->
      <q-select
        color="$primary"
        square
        filled
        v-model="platform"
        :options="platforms"
        option-value="id"
        option-label="desc"
        class="select-games"
      >
        <template v-slot:prepend>
          <q-icon name="search" class="q-pl-sm" />
        </template>
      </q-select>
    </section>

    <!-- Sección que muestra el número de juegos encontrados -->
    <section>
      <p v-if="data.length > 0" class="q-pt-xs text-center">
        {{ data.length }} juegos encontrados.
      </p>
      <p v-else class="q-pt-xs text-center">
        <em>Selecciona una plataforma para buscar los juegos</em>
      </p>
    </section>

    <!-- Sección donde se carga el listado de juegos disponibles -->
    <section v-if="data.length > 0" class="q-mt-xl section-style row">
      <!-- Nota 1: <article></article> es equivalente a un <div> https://developer.mozilla.org/es/docs/Web/HTML/Element/article -->
      <!-- Nota 2: Bucle FOR directamente en HTML con Vue.js (https://es.vuejs.org/v2/guide/list.html) -->
      <div
        class="card-wrapper col-xs-12 col-sm-6 col-md-4 col-xl-3"
        v-for="item in data"
        :key="item.id"
      >
        <q-card padding class="my-card tarjeta">
          <img :src="item.thumbnail" class="img-tarjeta" />
          <q-card-section>
            <div class="text-h6 q-mb-xs text-white">{{ item.title }}</div>
            <div class="text-caption text-grey">
              {{ item.short_description }}
            </div>
            <div class="q-pt-md row no-wrap items-center q-gutter-sm">
              <q-badge outline color="color-font">{{ item.genre }}</q-badge>
              <q-badge outline color="accent">{{ item.platform }}</q-badge>
            </div>
          </q-card-section>
        </q-card>
      </div>

      <q-inner-loading :showing="loading">
        <q-spinner-gears size="50px" color="primary" />
      </q-inner-loading>
    </section>
  </q-page>
</template>

<script>
import { defineComponent, ref, watch } from "vue";
import { api } from "boot/axios";
import { useQuasar } from "quasar";
export default defineComponent({
  name: "IndexPage",
  setup() {
    const $q = useQuasar();
    const platform = ref(null);
    const data = ref([]);
    const loading = ref(false);

    const loadData = () => {
      loading.value = true;
      const params = new URLSearchParams([
        ["platform", platform.value.id],
        ["sort-by", "alphabetical"],
      ]);
      api
        .get("/api/games", {
          params,
          headers: {
            "X-RapidAPI-Key":
              "67d9436ce8msh5a7f6cb557501a3p1efcadjsn7e754104fc3a",
            "X-RapidAPI-Host": "free-to-play-games-database.p.rapidapi.com",
          },
        })
        .then((response) => {
          data.value = response.data;
          loading.value = false;
        })
        .catch(() => {
          $q.notify({
            color: "negative",
            position: "top",
            message: "Error al recuperar datos",
            icon: "report_problem",
          });
          loading.value = false;
        });
      console.log(data);
    };
    watch(platform, (newvalue, oldvalue) => {
      //console.log('filter by ', newvalue)

      loadData();
    });
    return {
      data,
      platform,
      platforms: [
        { id: "all", desc: "Mostrar todos" },
        { id: "pc", desc: "Ver juegos para PC" },
        { id: "browser", desc: "Ver juegos para Navegador web" },
      ],
      loading,
    };
  },
});
</script>
<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Montserrat:wght@400;900&display=swap");

.fondo {
  font-family: $font;
  color: $color-font;
}
.section-style {
  background-color: $secondary;
  color: $color-font;
  border: 2px solid $borde-seccion;
}

// SELECT
.select-games {
  background-color: $borde-seccion;
}
// CARDS
.card-wrapper {
  padding: 1rem;
}
.tarjeta {
  background-color: rgba(34, 34, 34, 0.107);
  border-radius: 0px;
}
.img-tarjeta {
  // margin: 10px;
  box-shadow: 0 0 $accent;
  transition: 0.25s;
  max-width: 100vw;
}
.img-tarjeta:hover {
  box-shadow: -5px 5px $accent, -4.5px 4.5px $accent, -4px 4px $accent,
    -3px 3px $accent, -2.5px 2.5px $accent, -1px 1px $accent,
    -0.5px 0.5px $accent;
  transform: translate(5px, -5px);
}
</style>
