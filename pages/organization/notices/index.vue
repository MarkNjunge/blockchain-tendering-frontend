<template>
  <div>
    <h1>Notices</h1>
    <p>{{notices}}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      notices: "Loading..."
    };
  },
  async mounted() {
    const res = await this.$axios({
      method: "get",
      url: "/api/notices/forCurrent"
    });

    this.notices = res.data;
  }
};
</script>