<template>
  <div>
    <h1>NOTICE BY ID</h1>
    <br />
    {{notice}}
    <br />
    <h1>BIDS</h1>
    <br />
    {{bids}}
  </div>
</template>

<script>
export default {
  data() {
    return {
      notice: null,
      bids: null
    };
  },
  async created() {
    const tenderId = this.$route.params.id;

    try {
      let res = await this.$axios({
        method: "get",
        url: `/api/notices/${tenderId}`
      });

      this.notice = res.data;

      res = await this.$axios({
        method: "get",
        url: `/api/bids?noticeId=${tenderId}`
      });

      this.bids = res.data;
    } catch (e) {
      console.log(e);
      alert(e.message);
    }
  }
};
</script>