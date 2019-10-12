<template>
  <div>
    <h1>ALL NOTICES</h1>
    <br />
    {{notices}}
  </div>
</template>

<script>
export default {
  data() {
    return {
      notices: null
    };
  },
  async asyncData({ $axios }) {
    try {
      const res = await $axios({
        method: "get",
        url: `/api/notices`
      });

      return {
        notices: res.data
      };
    } catch (e) {
      console.log(e);
      alert(e.message);
    }
  }
};
</script>