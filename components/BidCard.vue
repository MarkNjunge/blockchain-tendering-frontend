<template>
  <nuxt-link
    :to="'/bidder/notices/' + encodeURIComponent(bid.tenderNotice.tenderId)"
    class="p-6 bg-white rounded border border-gray-400 shadow cursor-pointer"
  >
    <div class="flex justify-between items-baseline">
      <h2 class="text-xl">{{bid.bidId}}</h2>
      <p class="tag bg-gray-200 text-gray-800">{{bid.status}}</p>
    </div>
    <p class="text-gray-700">{{bid.tenderNotice.tenderId}}</p>
    <div class="mt-4 flex justify-between items-baseline">
      <p class="text-gray-500">{{bid.datePosted}}</p>
      <p
        class="py-1 px-2 font-bold rounded bg-red-200 text-red-800 hover:bg-red-600 hover:text-black"
        @click="withdraw(bid)"
      >Withdraw</p>
    </div>
  </nuxt-link>
</template>

<script>
export default {
  name: "BidCard",
  props: ["bid"],
  methods: {
    withdraw(bid) {
      this.$parent.withdraw(bid);
    }
  }
};
</script>