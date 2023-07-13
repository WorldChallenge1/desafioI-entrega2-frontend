<template>
  <q-form @submit="onSubmit" @reset="onCancel" class="q-gutter-md">
    <q-input
      filled
      v-model="title"
      label="Post title *"
      lazy-rules
      :rules="[(val) => (val && val.length > 0) || 'Please type something']"
    />

    <q-input
      filled
      type="textarea"
      v-model="content"
      label="Post content *"
      lazy-rules
      :rules="[(val) => (val && val.length > 0) || 'Please type something']"
    />

    <div>
      <q-btn label="Submit" type="submit" color="primary" />
      <q-btn label="Cancel" type="reset" color="primary" flat class="q-ml-sm" />
    </div>
  </q-form>
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
  },

  data() {
    return {
      postId: null, // Propiedad para almacenar el ID del post
      post: {}, // Propiedad para almacenar el post
      title: "",
      content: "",
    };
  },
  created() {
    // Asignar el parámetro 'id' a la propiedad postId
    this.postId = this.$route.params.id;

    // Cargar post desde API
    api.get(`/posts/${this.postId}`).then((response) => {
      this.post = response.data;
      this.title = this.post.title;
      this.content = this.post.content;
    });
  },

  methods: {
    onSubmit() {
      console.log("Submitted");
      console.log(this.title + " " + this.content);
      if (this.title && this.content) {
        api
          .put(`/posts/${this.postId}`, {
            id: this.postId,
            title: this.title,
            content: this.content,
          })
          .then((response) => {
            this.$q.notify({
              color: "green-4",
              textColor: "white",
              icon: "cloud_done",
              message: `Modified ${this.title}`,
            });
            this.$router.push("/");
          })
          .catch((error) => {
            this.$q.notify({
              color: "red-4",
              textColor: "white",
              icon: "warning",
              message: error.message,
            });
          });
      } else {
        this.$q.notify({
          color: "red-4",
          textColor: "white",
          icon: "warning",
          message: "Please fill in all fields",
        });
      }
    },

    onCancel() {
      console.log("Cancel");
      this.$router.push("/");
    },
  },
};
</script>
