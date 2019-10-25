<template>
  <div class="card-no-p p-6 h-full">
    <div class="flex items-baseline">
      <h3 class="text-lg">{{notice.title}}</h3>
    </div>
    <p class="mt-1 px-1 bg-gray-200 rounded w-fit text-sm">{{notice.tenderId}}</p>
    <div class="mt-2 flex">
      <p class="text-gray-600">Deadline:</p>
      <p class="ml-2">{{notice.submissionClosingDate}}</p>
    </div>
    <p>Place bid</p>
  </div>
</template>

<script>
export default {
  name: "BidderNoticeCard",
  props: ["notice"]
};
</script>