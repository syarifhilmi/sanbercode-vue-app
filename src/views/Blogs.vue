<template>
  <v-app>
    <Alert />
    <Dialog />
    <v-container class="ma-0 pa-0" grid-list-sm>
      <v-subheader> All Blogs </v-subheader>
      <v-btn block color="primary" class="mb-1" @click="addBlog">
        <v-icon>mdi-pencil</v-icon>
        Tambah Blog
      </v-btn>
      <v-layout wrap>
        <blog-item-component
          v-for="blog in blogs"
          :key="`blog-` + blog.id"
          :blog="blog"
        ></blog-item-component>
      </v-layout>

      <v-pagination
        v-model="page"
        @input="go"
        :length="lengthPage"
        :total-visible="perPage"
      ></v-pagination>
    </v-container>
  </v-app>
</template>

<script>
import BlogItemComponent from "../components/BlogItemComponent.vue";
import { mapActions } from "vuex";
export default {
  components: {
    Alert: () => import("../components/Alert.vue"),
    Dialog: () => import("../components/Dialog.vue"),
    "blog-item-component": BlogItemComponent,
  },
  name: "App",
  data: () => ({
    apiDomain: "http://demo-api-vue.sanbercloud.com",
    blogs: [],
    page: 0,
    lengthPage: 0,
    perPage: 0,
  }),

  methods: {
    go() {
      const config = {
        method: "get",
        url: this.apiDomain + "/api/v2/blog?page=" + this.page,
      };
      this.axios(config)
        .then((response) => {
          let { blogs } = response.data;
          this.blogs = blogs.data;
          this.page = blogs.current_page;
          this.lengthPage = blogs.last_page;
          this.perPage = blogs.per_page;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    addBlog() {
      this.setDialogComponent({ component: "addBlog" });
    },
    ...mapActions({
      setAlert: "alert/set",
      setDialogComponent: "dialog/setComponent",
      setToken: "auth/setToken",
      setUser: "auth/setUser",
      checkToken: "auth/checkToken",
    }),
  },
  created() {
    this.go();
  },
};
</script>

