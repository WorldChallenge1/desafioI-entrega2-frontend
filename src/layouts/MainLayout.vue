<template>
  <q-layout view="lHh Lpr lFf">
    <q-header>
      <div class="q-px-lg q-pt-xl q-mb-md">
        <div class="text-h3">Posts</div>
        <div class="text-subtitle-1">{{ Todaysdate }}</div>
      </div>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from "vue";
import { date } from "quasar";

export default defineComponent({
  name: "MainLayout",

  computed: {
    Todaysdate() {
      let timestamp = Date.now();
      return date.formatDate(timestamp, "YYYY-MM-DD");
    },
  },

  setup() {
    const leftDrawerOpen = ref(false);

    return {
      leftDrawerOpen,
      toggleLeftDrawer() {
        leftDrawerOpen.value = !leftDrawerOpen.value;
      },
    };
  },
});
</script>
