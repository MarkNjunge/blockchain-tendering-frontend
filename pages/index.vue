<template>
  <div class="flex flex-col items-center">
    <logo />
    <nuxt-link :to="dashboard" class="mt-4 flex text-gray-500 hover:text-gray-800">
      <h3 class="font-bold mr-2">Proceed to dashboard</h3>
      <svg class="w-6 h-6 fill-current">
        <use href="#arrow-right" />
      </svg>
    </nuxt-link>
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
      dashboard: "#"
    };
  },
  mounted() {
    let session = window.localStorage.getItem("session");
    if (session) {
      session = JSON.parse(session);

      switch (session.participantType) {
        case "TenderingOrganization":
          this.dashboard = "/organization";
          break;
        case "TenderBidder":
          this.dashboard = "/bidder";
          break;
        default:
          break;
      }
    }
  }
};
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}
</style>
