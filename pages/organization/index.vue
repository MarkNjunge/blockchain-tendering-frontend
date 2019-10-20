<template>
  <div>
    <div id="recent-notices" class="bg-gray-200">
      <div class="pt-4 pb-4 container mx-auto">
        <h2 class="mt-4 section-title">Recent Tender Notices</h2>
        <div class="mt-2 recent-notices">
          <div v-for="(notice, index) in notices" :key="index">
            <nuxt-link :to="'/organization/notices/' + encodeURIComponent(notice.tenderId)">
              <NoticeCard :notice="notice" />
            </nuxt-link>
          </div>
          <nuxt-link to="/organization/notices/create">
            <div class="card h-full">
              <div class="flex flex-col h-full justify-center items-center">
                <svg class="h-16 w-16 fill-current text-gray-700">
                  <use href="#add" />
                </svg>
                <h2 class="text-gray-700 text-xl">Create a Notice</h2>
              </div>
            </div>
          </nuxt-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import NoticeCard from "@/components/NoticeCard";

export default {
  components: {
    NoticeCard
  },
  data() {
    return {
      participantType: null,
      notices: null
    };
  },
  async mounted() {
    let session = window.localStorage.getItem("session");
    if (!session) {
      console.log("Not logged in");
      this.$nuxt.$router.replace({ path: "/login" });
      return;
    }
    session = JSON.parse(session);

    this.participantType = session.participantType;

    const res = await this.$axios({
      method: "get",
      url: "/api/notices/forCurrent"
    });

    console.log(JSON.stringify(res.data, null, " "));

    this.notices = res.data
      // .sort((a, b) => a.closingDate > b.closingDate)
      // .reverse()
      .map(n => {
        const d = new Date(n.submissionClosingDate);
        const options = { timeStyle: "short", dateStyle: "long" };
        n.submissionClosingDate = d.toLocaleDateString("en-US", options);
        return { ...n };
      })
      .slice(0, 2);
  },
  methods: {
    async logout() {
      const res = await this.$axios({
        method: "post",
        url: "/api/auth/logout"
      });

      window.localStorage.removeItem("session");
      window.localStorage.removeItem("profile");
      this.$nuxt.$router.replace({ path: "/login" });
    }
  }
};
</script>

<style lang="scss">
.recent-notices {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 1rem;
}
</style>