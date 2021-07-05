<template>
  <v-card>
    <v-toolbar dark color="success">
      <v-btn icon dark @click.native="close">
        <v-icon>mdi-close</v-icon>
      </v-btn>
      <v-toolbar-title>uploadFoto</v-toolbar-title>
    </v-toolbar>
    <v-container fluid>
      <v-form ref="form">
        <input type="file" @change="onSelected" />
        <div class="text-xs-center mt-4">
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
      apiDomain: "http://demo-api-vue.sanbercloud.com",
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
    onSelected(event) {
      this.selected = event.target.files[0];
    },
    submit() {
      let { id } = this.$route.params;
      let formData = new FormData();
      formData.append("photo", this.selected, this.selected.name);
      console.log(this.token);
      const config = {
        method: "post",
        url: this.apiDomain + `/api/v2/blog/${id}/upload-photo`,
        data: formData,
        headers: {
          Authorization: "Bearer" + this.token,
        },
      };
      this.axios(config)
        .then((response) => {
          console.log(response.data);
          this.setAlert({
            status: true,
            color: "success",
            text: "Foto berhasil diupdate",
          });
          this.close();
          window.location.reload();
        })
        .catch((error) => {
          console.log(error);
          this.setAlert({
            status: true,
            color: "error",
            text: "Foto tidak berhasil diupdate",
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