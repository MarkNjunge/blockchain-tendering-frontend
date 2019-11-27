<template>
  <div>
    <div id="page-bg" class="bg-gray-100"></div>
    <symbols></symbols>
    <div class="nav bg-white py-3 shadow border-b-2 border-blue-400 text-gray-800">
      <div class="container mx-auto flex justify-between items-center">
        <nuxt-link :to="homePageUrl" class="flex items-center">
          <svg class="w-10 h-10 fill-current">
            <use href="#logo" />
          </svg>
          <p class="ml-4 text-xl">BLOCK-T</p>
        </nuxt-link>
        <div class="flex items-center" v-if="orgName">
          <div class="mr-6" v-if="participantType == 'TenderBidder'">
            <nuxt-link
              to="/bidder/notices"
              class="nav-link mr-2 text-sm text-gray-600 hover:text-blue-700 border-b-2 border-white hover:border-blue-700"
            >NOTICES</nuxt-link>
            <nuxt-link
              to="/bidder/bids"
              class="nav-link mr-2 text-sm text-gray-600 hover:text-blue-700 border-b-2 border-white hover:border-blue-700"
            >BIDS</nuxt-link>
          </div>
          <div class="mr-6" v-if="participantType == 'TenderingOrganization'">
            <nuxt-link
              to="/organization/notices"
              class="nav-link mr-2 text-sm text-gray-600 hover:text-blue-700 border-b-2 border-white hover:border-blue-700"
            >NOTICES</nuxt-link>
            <nuxt-link
              to="/organization/notices/create"
              class="nav-link mr-2 text-sm text-gray-600 hover:text-blue-700 border-b-2 border-white hover:border-blue-700"
            >CREATE NOTICE</nuxt-link>
          </div>
          <p class>{{orgName}}</p>
          <div class="ml-4 w-8 h-8 bg-white rounded-full border border-gray-400 bg-gray-200"></div>
        </div>
      </div>
    </div>

    <nuxt id="page-content" />
  </div>
</template>

<script>
import Symbols from "../components/Symbols";

export default {
  components: { Symbols },
  data() {
    return {
      orgName: null,
      profilePicData: null,
      homePageUrl: "#",
      participantType: null
    };
  },
  async mounted() {
    let profile = window.localStorage.getItem("profile");
    if (profile) {
      profile = JSON.parse(profile);
      this.orgName = profile.name;
    }

    let session = window.localStorage.getItem("session");
    if (session) {
      session = JSON.parse(session);
      this.participantType = session.participantType;
      console.log(this.participantType);
      if (session.participantType == "TenderingOrganization") {
        this.homePageUrl = "/organization";
      } else if (session.participantType == "TenderBidder") {
        this.homePageUrl = "/bidder";
      }
    }
  }
};
</script>

<style lang="scss">
@import "../assets/style/main";
@import url("https://fonts.googleapis.com/css?family=Lato:900|Roboto&display=swap");

html {
  font-family: "Roboto", sans-serif;
}

.nav {
  font-family: "Lato", sans-serif;
}

#page-content {
  position: relative;
}

#page-bg {
  position: fixed;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  z-index: -999;
}
</style>
