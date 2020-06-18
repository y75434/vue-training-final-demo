<template>
  <div>
    <Sidebar />
    <div class="container-fluid">
      <div class="row">
        <main
          role="main"
          class="col-md-12 ml-sm-auto px-4"
        >
          <router-view />
        </main>
      </div>
    </div>
  </div>
</template>

<script>
import Sidebar from '@/components/backend/Sidebar.vue';

export default {
  name: 'Dashboard',
  components: {
    Sidebar,
  },
  data() {
    return {
      uuid: process.env.VUE_APP_UID,
    };
  },
  created() {
    const token = this.$cookies.get('token');

    const url = `${process.env.VUE_APP_APIPATH}/api/auth/check`;

    this.setAxiosAuthorization();

    this.$http.post(url, { api_token: token }).then((response) => {
      if (!response.data.success) {
        this.$router.push({
          path: 'login',
        });

        this.$bus.$emit('message:push',
          `出現錯誤惹，好糗Σ( ° △ °|||)︴
            ${response.data.message}`,
          'danger');
      }
    });
  },
};
</script>

<style lang="scss">
</style>
