<template>
  <transition name="fade">
    <div
      class="mt-4 w-full h-full flex flex-col items-center justify-center"
      v-if="visible == true"
    >
      <svg class="w-16 h-16 fill-current text-blue-700">
        <use href="#loader" />
      </svg>
      <p class="mt-4 text-xl text-blue-800">Loading...</p>
    </div>
  </transition>
</template>

<script>
export default {
  name: "loading",
  props: ["visible"]
};
</script>

<style lang="css">
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>