<template>
  <div class="container mx-auto px-4 xl:w-1/2 md:w-4/5">
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
