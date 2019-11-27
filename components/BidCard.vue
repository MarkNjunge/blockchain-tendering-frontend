<template>
  <nuxt-link
    :to="'/bidder/notices/' + encodeURIComponent(bid.tenderNotice.tenderId)"
    class="p-6 bg-white rounded border border-gray-400 shadow cursor-pointer"
  >
    <div class="flex justify-between items-baseline">
      <h2 class="text-xl">{{bid.bidId}}</h2>
      <div class>
        <p class="tag tag-gray" v-if="bid.status == 'ACTIVE'">{{bid.status}}</p>
        <p class="tag tag-green" v-else-if="bid.status == 'ACCEPTED'">{{bid.status}}</p>
        <p class="tag tag-orange" v-else-if="bid.status == 'WITHDRAWN'">{{bid.status}}</p>
        <p class="tag tag-red" v-else-if="bid.status == 'REJECTED'">{{bid.status}}</p>
      </div>
    </div>
    <div class="flex items-center">
      <div class="h-4 w-4 bg-gray-500 rounded-full" v-if="bid.tenderNotice.status =='PUBLISHED'"></div>
      <div class="h-4 w-4 bg-red rounded-full" v-else-if="bid.tenderNotice.status =='CLOSED'"></div>
      <div
        class="h-4 w-4 bg-green-500 rounded-full"
        v-else-if="bid.tenderNotice.status =='AWARDED'"
      ></div>
      <div class="h-4 w-4 bg-orange rounded-full" v-else-if="bid.tenderNotice.status =='WITHDRAWN'"></div>

      <p class="ml-2 text-gray-700">{{bid.tenderNotice.tenderId}}</p>
    </div>
    <p class="mt-4 text-gray-700">Posted on {{bid.datePosted}}</p>
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