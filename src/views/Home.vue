<template>
  <div class="about">
    <h1>This is an home page</h1>
  </div>
</template>

<script>
export default {
  created() {
    const api = "http://demo-api-vue.sanbercloud.com/api/v2/blog/random/3";
    this.axios.get(api).then((response) => {
      console.log(response.data);
    });
  },
};
</script>

