<template>
  <div>
    <p>Logging out...</p>
  </div>
</template>

<script>
export default {
  async mounted() {
    if (!window.localStorage.getItem("session")) {
      console.log("Not logged in");
      this.$nuxt.$router.replace({ path: "/login" });
      return;
    }

    const res = await this.$axios({
      method: "post",
      url: "/api/auth/logout"
    });

    window.localStorage.removeItem("session");
    this.$nuxt.$router.replace({ path: "/login" });
  }
};
</script>