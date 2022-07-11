<template>
  <q-layout view="hHh lpR fFf" class="bg-primary">
    <q-header bordered elevated class="bg-primary text-white borde-oscuro">
      <q-toolbar>
        <q-toolbar-title class="q-pt-sm">
          <img
            src="https://www.freetogame.com/assets/images/freetogame-logo.png"
          />
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from "vue";

export default defineComponent({
  name: "MainLayout",

  components: {},

  setup() {
    return {};
  },
});
</script>
<style lang="scss">
.fondo-general {
  background-color: $primary;
}
.borde-oscuro {
  color: $primary;
}
</style>
