<template>
  <div>
    <h1>Dashboard</h1>
    <nuxt-link to="/profile">Profile</nuxt-link>
    <p @click="logout">Logout</p>
    <p v-if="participantType">Logged in as {{participantType}}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      participantType: null
    };
  },
  mounted() {
    let session = window.localStorage.getItem("session");
    if (!session) {
      console.log("Not logged in");
      this.$nuxt.$router.replace({ path: "/login" });
      return;
    }
    session = JSON.parse(session);

    this.participantType = session.participantType;
  },
  methods: {
    async logout() {
      const res = await this.$axios({
        method: "post",
        url: "/api/auth/logout"
      });

      window.localStorage.removeItem("session");
      window.localStorage.removeItem("profile");
      this.$nuxt.$router.replace({ path: "/login" });
    }
  }
};
</script>