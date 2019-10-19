<template>
  <div>
    <div id="page-bg" class="bg-gray-100"></div>
    <symbols></symbols>
    <nuxt id="page-content" class="pt-4" />
  </div>
</template>

<script>
import Symbols from "../components/Symbols";
export default {
  components: { Symbols },
  data() {
    return {
      orgName: null
    };
  },
  mounted() {
    let session = window.localStorage.getItem("session");
    if (session) {
      session = JSON.parse(session);

      this.orgName = session.participantId;
      return;
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
