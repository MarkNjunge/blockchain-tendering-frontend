<template>
  <div class="center-content">
    <h2 class="section-title">Blockchain Network</h2>
    <div class v-if="blocks">
      <div
        class="bg-white rounded border border-gray-400 shadow mb-4"
        v-for="(block, index) in blocks"
        :key="index"
      >
        <div class="flex p-4">
          <p class="text-xl">{{block.number}}</p>
          <div class="ml-4 flex flex-col">
            <div class="flex">
              <p class="text-gray-700 mr-2">Block hash:</p>
              <p>{{block.hash}}</p>
            </div>
            <div class="flex">
              <p class="text-gray-700 mr-2">Previous hash:</p>
              <p>{{block.previousHash}}</p>
            </div>
          </div>
        </div>
        <div class="p-4 bg-gray-200">
          <p class="text-sm w-full text-gray-800 break-words">{{block.data}}</p>
        </div>
      </div>
    </div>
    <Loading :visible="loading" />
  </div>
</template>

<script>
import Loading from "@/components/Loading.vue";

export default {
  layout: "no-bar",
  components: {
    Loading
  },
  data() {
    return {
      loading: false,
      blocks: null
    };
  },
  async mounted() {
    this.loading = true;
    const res = await this.$axios({
      method: "GET",
      url: "/api/network"
    });

    this.loading = false;
    this.blocks = res.data;
  }
};
</script>

<style>
</style>
