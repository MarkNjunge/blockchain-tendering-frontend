<template>
  <div>
    <h1>NOTICE BY ID</h1>
    <br />
    {{notice}}
  </div>
</template>

<script>
export default {
  data() {
    return {
      notice: null
    };
  },
  async created() {
    const noticeId = this.$route.params.id;

    try {
      const res = await this.$axios({
        method: "get",
        url: `/api/notices/${noticeId}`
      });

      this.notice = res.data;
    } catch (e) {
      console.log(e);
      alert(e.message);
    }
  }
};
</script>