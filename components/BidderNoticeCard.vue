<template>
  <nuxt-link
    :to="'/bidder/notices/' + encodeURIComponent(notice.tenderId)"
    class="card-no-p p-6 h-full"
  >
    <div class="flex items-baseline">
      <h3 class="text-lg">{{notice.title}}</h3>
    </div>
    <p class="mt-1 px-1 bg-gray-200 rounded w-fit text-sm">{{notice.tenderId}}</p>
    <p class="mt-2">Bids due {{notice.submissionClosingDate}}</p>
  </nuxt-link>
</template>

<script>
export default {
  name: "BidderNoticeCard",
  props: ["notice"]
};
</script>