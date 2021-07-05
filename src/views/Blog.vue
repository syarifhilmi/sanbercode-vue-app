<template>
  <v-app>
    <Alert />
    <Dialog />
    <div>
      <v-card v-if="blog.id">
        <v-img
          :src="
            blog.photo
              ? apiDomain + blog.photo
              : 'https://picsum.photos/200/300'
          "
          class="white--text"
          height="200px"
        >
          <v-card-title
            class="fill-height align-end"
            v-text="blog.title"
          ></v-card-title>
        </v-img>
        <v-card-text>
          <v-simple-table v-if="!update" dense>
            <tbody>
              <tr>
                <td><v-icon>mdi-format-title</v-icon>Judul</td>
                <td class="blue--text">{{ blog.title }}</td>
                <td>
                  <v-btn
                    block
                    color="secondary"
                    class="mb-1"
                    @click="uploadFoto"
                  >
                    Upload Foto
                  </v-btn>
                  <v-btn
                    block
                    color="error"
                    class="mb-1"
                    @click="removeBlog(blog.id)"
                  >
                    Hapus
                  </v-btn>
                </td>
              </tr>
              <tr>
                <td><v-icon>mdi-note</v-icon>Deskripsi</td>
                <td>{{ blog.description }}</td>
              </tr>
            </tbody>
          </v-simple-table>
          <v-simple-table v-if="update" dense>
            <tbody>
              <tr>
                <td><v-icon>mdi-format-title</v-icon>Judul</td>
                <td class="blue--text">
                  <v-text-field v-model="judul" required></v-text-field>
                </td>
              </tr>
              <tr>
                <td><v-icon>mdi-note</v-icon>Deskripsi</td>
                <td><v-textarea v-model="deskripsi" required></v-textarea></td>
              </tr>
            </tbody>
          </v-simple-table>
        </v-card-text>
      </v-card>
      <div class="mt-4" v-if="!update">
        <v-btn block color="secondary" class="mb-1" @click="update = true">
          Edit
        </v-btn>
      </div>
      <div class="mt-4" v-if="update">
        <v-btn block color="secondary" class="mb-1" @click="edit(blog.id)">
          Submit Edit
        </v-btn>
        <v-btn block color="error" class="mb-1" @click="update = false">
          Batalkan
        </v-btn>
      </div>
    </div>
  </v-app>
</template>

<script>
import { mapGetters, mapActions } from "vuex";
export default {
  data: () => ({
    apiDomain: "https://demo-api-vue.sanbercloud.com",
    blog: {},
    update: false,
    judul: "",
    deskripsi: "",
  }),
  computed: {
    ...mapGetters({
      token: "auth/token",
      Alert: () => import("../components/Alert.vue"),
      Dialog: () => import("../components/Dialog.vue"),
    }),
  },
  name: "App",
  methods: {
    go() {
      let { id } = this.$route.params;

      const config = {
        method: "get",
        url: `${this.apiDomain}/api/v2/blog/${id}`,
      };
      this.axios(config)
        .then((response) => {
          let { blog } = response.data;
          console.log(blog.photo);
          this.blog = blog;
          this.judul = blog.title;
          this.deskripsi = blog.description;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    removeBlog(id) {
      console.log(id);
      const config = {
        method: "post",
        url: `${this.apiDomain}/api/v2/blog/${id}?_method=DELETE`,
        headers: {
          Authorization: "Bearer" + this.token,
        },
      };
      this.axios(config)
        .then((response) => {
          console.log(response);
          window.location = "/blogs";
          this.setAlert({
            status: true,
            color: "success",
            text: "Blog berhasil dihapus",
          });
        })
        .catch((error) => {
          console.log(error);
          this.setAlert({
            status: true,
            color: "error",
            text: "Blog gagal dihapus",
          });
        });
    },
    uploadFoto() {
      this.setDialogComponent({ component: "upload" });
    },
    edit(id) {
      console.log(id);
      const config = {
        method: "post",
        url: `${this.apiDomain}/api/v2/blog/${id}?_method=PUT`,
        headers: {
          Authorization: "Bearer" + this.token,
          "content-type": "application/json",
          Accept: "application/json",
        },
        data: {
          title: this.judul,
          description: this.deskripsi,
        },
      };
      this.axios(config)
        .then((response) => {
          console.log(response);
          window.location.reload();
          this.setAlert({
            status: true,
            color: "success",
            text: "Blog berhasil diedit",
          });
        })
        .catch((error) => {
          console.log(error);
          this.setAlert({
            status: true,
            color: "error",
            text: "Blog gagal diedit",
          });
        });
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

