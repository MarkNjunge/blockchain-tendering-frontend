<template>
  <div>{{bids}}</div>
</template>

<script>
export default {
  data() {
    return {
      bids: "Loading..."
    };
  },
  async asyncData({ $axios }) {
    const res = await $axios.$get("/api/bids/forCurrent");

    return {
      bids: res
    };
  }
};
</script>