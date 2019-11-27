<template>
  <div class="center-content-form">
    <logo />
    <h1 class="section-title mt-2">Upload your card to login</h1>
    <div class="card mt-2 mb-8">
      <div class="input-group">
        <label for="card" class="input-label">Card File</label>
        <input type="file" name="card" id="card" @change="onFileSelected" class="input-field" />
      </div>
      <button @click="upload" class="mt-6 w-full btn">Login</button>
    </div>
  </div>
</template>

<script>
import Logo from "~/components/Logo.vue";

export default {
  layout: "no-bar",
  components: {
    Logo
  },
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

        const session = res.data.meta.session;
        window.localStorage.setItem("session", JSON.stringify(session));

        const profileRes = await this.$axios({
          method: "get",
          url: "/api/profile"
        });

        window.localStorage.setItem("profile", JSON.stringify(profileRes.data));

        switch (session.participantType) {
          case "TenderingOrganization":
            this.$nuxt.$router.replace({ path: "/organization" });
            break;
          case "TenderBidder":
            this.$nuxt.$router.replace({ path: "/bidder" });
            break;
          default:
            this.$nuxt.$router.replace({ path: "/" });
            break;
        }
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
