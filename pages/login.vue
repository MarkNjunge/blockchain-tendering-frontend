<template>
  <div class>
    <h1>Login</h1>
    <input type="file" name="card" id="card" @change="onFileSelected" />
    <button @click="upload">Upload</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedFile: null
    };
  },
  methods: {
    onFileSelected(event) {
      this.selectedFile = event.target.files[0];
    },
    async upload() {
      const bodyFormData = new FormData();
      bodyFormData.append("card", this.selectedFile);
      try {
        const res = await this.$axios({
          method: "post",
          url: "/api/auth/login",
          data: bodyFormData,
          config: { headers: { "Content-Type": "multipart/form-data" } }
        });
        console.log("Logged in!");

        const session = res.data.meta.session;

        window.localStorage.setItem("session", JSON.stringify(session));

        this.$nuxt.$router.replace({ path: "/dashboard" });
      } catch (e) {
        console.log(e);
        alert(e.message);
      }
    }
  }
};
</script>

<style>
</style>
