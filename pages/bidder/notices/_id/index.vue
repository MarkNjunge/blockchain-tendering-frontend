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
      noticeId: null,
      notice: null
    };
  },
  async created() {
    this.noticeId = decodeURIComponent(this.$route.params.id);
    const encodedNoticeId = encodeURIComponent(this.noticeId);

    try {
      const res = await this.$axios({
        method: "get",
        url: `/api/notices/${encodedNoticeId}`
      });

      this.notice = res.data;
    } catch (e) {
      console.log(e);
      alert(e.message);
    }
  }
};
</script>