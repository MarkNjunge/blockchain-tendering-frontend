<template>
  <div>{{bids}}</div>
</template>

<script>
export default {
  data() {
    return {
      bids: null
    };
  },
  async created() {
    const tenderId = this.$route.params.id;

    try {
      const res = await this.$axios({
        method: "get",
        url: `/api/bids?tenderId=${tenderId}`
      });

      this.bids = res.data;
    } catch (e) {
      console.log(e);
      alert(e.message);
    }
  }
};
</script>