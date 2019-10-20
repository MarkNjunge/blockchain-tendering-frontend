<template>
  <div>
    <div id="page-bg" class="bg-gray-100"></div>
    <symbols></symbols>
    <div class="nav bg-white py-3 shadow border-b-2 border-blue-400 text-gray-800">
      <div class="container mx-auto flex justify-between items-center">
        <div class="flex items-center">
          <svg class="w-10 h-10 fill-current">
            <use href="#logo" />
          </svg>
          <p class="ml-4 text-xl">BLOCK-T</p>
        </div>
        <div class="flex items-center" v-if="orgName">
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
      profilePicData: null
    };
  },
  async mounted() {
    let profile = window.localStorage.getItem("profile");
    if (profile) {
      profile = JSON.parse(profile);
      this.orgName = profile.name;
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
