<template>
  <v-card>
    <v-toolbar dark color="success">
      <v-btn icon dark @click.native="close">
        <v-icon>mdi-close</v-icon>
      </v-btn>
      <v-toolbar-title>Register</v-toolbar-title>
    </v-toolbar>
    <v-divider></v-divider>
    <v-container fluid>
      <v-form ref="form">
        <v-text-field
          v-model="email"
          label="E-mail"
          required
          append-icon="mdi-email"
        ></v-text-field>
        <v-text-field
          v-model="password"
          label="Password"
          :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
          :type="showPassword ? 'text' : 'password'"
          @click:append="showPassword = !showPassword"
        ></v-text-field>
        <v-text-field v-model="name" label="Nama"></v-text-field>
        <input type="file" @change="onSelected" />
        <div class="text-xs-center">
          <v-btn color="success lighten-1" @click="submit"> Submit </v-btn>
        </div>
      </v-form>
    </v-container>
  </v-card>
</template>

<script>
import { mapActions } from "vuex";
export default {
  data() {
    return {
      email: "",
      showPassword: false,
      password: "",
      name: "",
      selected: null,
      apiDomain: "http://demo-api-vue.sanbercloud.com",
    };
  },
  methods: {
    ...mapActions({
      setAlert: "alert/set",
    }),
    onSelected(event) {
      this.selected = event.target.files[0];
    },
    close() {
      this.$emit("closed", false);
    },
    submit() {
      console.log(this.name);

      let formData = new FormData();
      formData.append("photo_profile", this.selected, this.selected.name);
      formData.append("email", this.email);
      formData.append("password", this.password);
      formData.append("name", this.name);
      const config = {
        method: "post",
        url: this.apiDomain + "/api/v2/auth/register",
        headers: {
          "content-type": "application/json",
          Accept: "application/json",
        },
        data: formData,
      };
      this.axios(config)
        .then((response) => {
          console.log(response.data);
          this.setAlert({
            status: true,
            color: "success",
            text: "Register Berhasil",
          });
          this.close();
        })
        .catch((error) => {
          console.log(error);
          this.setAlert({
            status: true,
            color: "error",
            text: "Register Gagal",
          });
        });
    },
  },
};
</script>