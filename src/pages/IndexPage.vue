<template>
  <div class="row">
    <div class="q-pa-md col-6 offset-3">
      <q-form @submit="onSubmit" @reset="onReset" class="q-gutter-md">
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
          <q-btn
            label="Reset"
            type="reset"
            color="primary"
            flat
            class="q-ml-sm"
          />
        </div>
      </q-form>
    </div>
  </div>

  <q-page class="row items-center justify-evenly">
    <q-list separator>
      <transition-group
        appear
        enter-active-class="animated fadeIn slow"
        leave-active-class="animated fadeOut slow"
      >
        <q-item v-for="post in posts" :key="post.id" class="custom-item">
          <q-item-section>
            <q-item-label class="text-subtitle1">
              {{ post.title }}
            </q-item-label>
            <q-item-label class="text-body1" @click="seePost(post)">{{
              post.content
            }}</q-item-label>
            <div class="row justify-between q-mt-sm">
              <q-btn
                @click="seePost(post)"
                color="grey"
                icon="preview"
                size="sm"
                flat
                round
              />
              <q-btn
                @click="editPost(post)"
                color="grey"
                icon="edit"
                size="sm"
                flat
                round
              />
              <q-btn
                @click="deletePost(post)"
                color="red"
                icon="delete"
                size="sm"
                flat
                round
              />
            </div>
          </q-item-section>
        </q-item>
      </transition-group>
    </q-list>
  </q-page>
</template>

<script>
import { defineComponent } from "vue";
import { useQuasar } from "quasar";
import { ref } from "vue";
import { api } from "src/boot/axios";

// const posts = [
//   {
//     id: 1,
//     title: "Post 1",
//     content: "Post 1 content",
//   },
// ];

export default defineComponent({
  data() {
    api.get("/posts").then((response) => {
      if (response.status === 200) {
        this.posts = response.data;
      } else {
        this.posts = [];
      }
    });
    return {
      posts: [],
      title: "",
      content: "",
    };
  },

  setup() {
    const $q = useQuasar();
  },
  name: "IndexPage",
  methods: {
    onSubmit() {
      // console.log(this.title + " " + this.content);
      if (this.title && this.content) {
        api
          .post("/posts", {
            title: this.title,
            content: this.content,
          })
          .then((response) => {
            const post = response.data;
            this.$q.notify({
              color: "green-4",
              textColor: "white",
              icon: "cloud_done",
              message: `Created ${post.title}`,
            });
            this.posts.push(post);
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

    onReset() {
      this.title = "";
      this.content = "";
    },

    deletePost(post) {
      api
        .delete(`/posts/${post.id}`)
        .then((response) => {
          this.posts = this.posts.filter((p) => p.id !== post.id);
          this.$q.notify({
            color: "red-4",
            textColor: "white",
            icon: "warning",
            message: `Deleted ${post.title}`,
          });
        })
        .catch((error) => {
          console.log(error);
        });
    },
    editPost(post) {
      this.$router.push(`/post/edit/${post.id}`, { id: post.id });
    },
    seePost(post) {
      // code here
      this.$router.push(`/post/${post.id}`, { id: post.id });
    },
  },
});
</script>

<style lang="sass" scoped>
.custom-item
  border: 1px solid black
</style>
