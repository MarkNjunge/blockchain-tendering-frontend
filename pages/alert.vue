<template>
  <div>
    <!--  -->
    <transition name="fade">
      <div v-if="hasError" class="absolute w-screen flex justify-center">
        <div
          class="inline-block p-2 bg-red-100 rounded border border-red-700 text-red-700 font-bold flex items-center"
        >
          <svg class="h-5" viewBox="0 0 192 512">
            <path
              fill="currentColor"
              d="M176 432c0 44.112-35.888 80-80 80s-80-35.888-80-80 35.888-80 80-80 80 35.888 80 80zM25.26 25.199l13.6 272C39.499 309.972 50.041 320 62.83 320h66.34c12.789 0 23.331-10.028 23.97-22.801l13.6-272C167.425 11.49 156.496 0 142.77 0H49.23C35.504 0 24.575 11.49 25.26 25.199z"
              class
            />
          </svg>
          <p class="ml-2">Lorem ipsum dolor sit amet consectetur adipisicing elit. Ea, dignissimos?</p>
        </div>
      </div>
    </transition>
    <!--  -->
    <div class="container mx-auto mt-4">
      <h1 class="text-lg">Lorem, ipsum.</h1>
      <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Ullam, minima enim, deleniti quam labore quae sunt, accusantium blanditiis asperiores quas eligendi aliquam eveniet. Repudiandae optio ipsum, ullam facere commodi ex?</p>
      <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Facilis eius cumque cupiditate delectus maxime tempora, assumenda consequuntur, perspiciatis aliquid asperiores possimus voluptatum accusantium laudantium ab eveniet repellat consectetur natus quibusdam, at facere mollitia sed. Officia, dolor. Possimus consequatur laudantium quod cum quam. Maxime est similique dolorum vero eum, fugiat ratione!</p>
      <button class @click="showError">Button</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      hasError: false
    };
  },
  methods: {
    showError() {
      this.hasError = true;

      setTimeout(() => {
        this.hasError = false;
      }, 6000);
    }
  }
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

button {
  /* @apply py-1;
  @apply px-3;
  @apply mt-3; */
  /* @apply rounded; */
  @apply --bg-blue-600;
  /* @apply text-white;
  @apply shadow;
  @apply hover:shadow-lg; */
}
</style>