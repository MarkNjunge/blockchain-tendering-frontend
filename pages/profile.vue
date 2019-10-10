<template>
  <div>{{profile}}</div>
</template>

<script>
export default {
  data() {
    return {
      profile: "loading"
    };
  },
  async mounted() {
    if (!window.localStorage.getItem("session")) {
      console.log("Not logged in");
      this.$nuxt.$router.replace({ path: "/login" });
      return;
    }

    try {
      const res = await this.$axios({
        method: "get",
        url: "/api/profile"
      });

      this.profile = res.data;
    } catch (e) {
      console.log(e.message);
      console.log(e.response.data.message);
      this.profile = e.response.data.message;
    }
  },
  async asyncData({ $axios }) {}
};
</script>