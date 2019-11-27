<template>
  <div
    class="p-4 bg-white rounded border border-gray-203 flex flex-col items-start dashboard-widget leading-tight"
  >
    <svg class="w-6 h-6 text-blue-600">
      <use :href="'#' + icon" />
    </svg>
    <p class="mt-1 text-gray-800 text-2xl">{{number}}</p>
    <p class="text-gray-700 text-sm">{{text}}</p>
  </div>
</template>

<script>
export default {
  name: "DashboardWidged",
  props: ["number", "text", "icon"]
};
</script>

<style lang="scss">
.dashboard-widget {
  width: 160px;
}
</style>