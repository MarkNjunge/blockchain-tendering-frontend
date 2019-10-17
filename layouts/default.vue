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
  components: { Symbols }
};
</script>

<style lang="scss">
@import "../assets/style/main";

#page-content {
  position: relative;
}

#page-bg {
  position: fixed;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}
</style>
