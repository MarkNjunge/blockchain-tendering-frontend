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
  async mounted() {
    const res = await this.$axios({
      method: "GET",
      url: "/api/bids/forCurrent"
    });

    this.bids = res.data;
  }
};
</script>