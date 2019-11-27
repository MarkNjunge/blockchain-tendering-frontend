<template>
  <div class="card-no-p p-6 h-full">
    <div class="flex items-baseline">
      <h3 class="text-lg">{{notice.title}}</h3>
    </div>
    <p class="mt-1 px-1 bg-gray-200 rounded w-fit text-sm">{{notice.tenderId}}</p>
    <div class="mt-2 flex items-baseline">
      <p class="text-xl">34</p>
      <p class="ml-1">bids placed</p>
    </div>
    <p class="mt-1">Bids due {{notice.submissionClosingDate}}</p>
  </div>
</template>

<script>
export default {
  name: "NoticeCard",
  props: ["notice"]
};
</script>