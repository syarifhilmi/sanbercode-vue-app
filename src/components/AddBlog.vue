<template>
  <v-card>
    <v-toolbar dark color="success">
      <v-btn icon dark @click.native="close">
        <v-icon>mdi-close</v-icon>
      </v-btn>
      <v-toolbar-title>Tambah Blog</v-toolbar-title>
    </v-toolbar>
    <v-divider></v-divider>
    <v-container fluid>
      <v-form ref="form">
        <v-text-field v-model="judul" label="Judul" required></v-text-field>
        <v-textarea v-model="deskripsi" label="Deskripsi" required></v-textarea>
        <div class="text-xs-center">
          <v-btn color="success lighten-1" @click="submit"> Submit </v-btn>
        </div>
      </v-form>
    </v-container>
  </v-card>
</template>

<script>
import { mapActions, mapGetters } from "vuex";
export default {
  data() {
    return {
      judul: "",
      deskripsi: "",
      apiDomain: "https://demo-api-vue.sanbercloud.com",
    };
  },
  computed: {
    ...mapGetters({
      token: "auth/token",
    }),
  },
  methods: {
    ...mapActions({
      setAlert: "alert/set",
    }),
    close() {
      this.$emit("closed", false);
    },
    submit() {
      console.log(this.token);
      const config = {
        method: "post",
        url: this.apiDomain + "/api/v2/blog",
        data: {
          title: this.judul,
          description: this.deskripsi,
        },
        headers: {
          Authorization: "Bearer" + this.token,
          "content-type": "application/json",
          Accept: "application/json",
        },
      };
      this.axios(config)
        .then((response) => {
          console.log(response.data);
          this.setAlert({
            status: true,
            color: "success",
            text: "Blog Berhasil Ditambahkan",
          });
          this.close();
        })
        .catch((error) => {
          console.log(error);
          this.setAlert({
            status: true,
            color: "error",
            text: "Blog Tidak Berhasil Ditambahkan",
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
};
</script>