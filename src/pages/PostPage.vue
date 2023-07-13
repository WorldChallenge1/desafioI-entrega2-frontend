<template>
  <div class="q-pa-md row items-start q-gutter-md justify-center">
    <q-card dark bordered class="bg-grey-9 my-card">
      <q-card-section>
        <div class="text-h1">{{ post.title }}</div>
        <div class="text-subtitle">{{ post.content }}</div>
      </q-card-section>

      <q-separator dark inset />
    </q-card>
  </div>
  <div class="q-pa-md row items-start q-gutter-md justify-center">
    <q-btn color="primary" icon="check" label="Home" @click="onClick" />
  </div>
</template>

<script>
import { defineComponent } from "vue";
import { useQuasar } from "quasar";
import { useRouter } from "vue-router";
import { ref } from "vue";
import { api } from "src/boot/axios";

export default {
  setup() {
    const $q = useQuasar();
    const $router = useRouter();
    return {
      onClick() {
        // return to the IndexPage
        console.log("Clicked");
        $router.push("/");
      },
    };
  },

  data() {
    return {
      postId: null, // Propiedad para almacenar el ID del post
      post: {}, // Propiedad para almacenar el post
    };
  },
  created() {
    // Asignar el parámetro 'id' a la propiedad postId
    this.postId = this.$route.params.id;

    // Cargar post desde API
    api.get(`/posts/${this.postId}`).then((response) => {
      this.post = response.data;
    });
  },
};
</script>
