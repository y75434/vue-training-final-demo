<template>
  <div class="container">
    <Loading :active.sync="isLoading" />
    <form
      class="form-signin"
      @submit.prevent="signin"
    >
      <h1 class="h3 mb-3 font-weight-normal">
        請先登入
      </h1>
      <div class="form-group">
        <label
          for="inputEmail"
          class="sr-only"
        >Email address</label>
        <input
          id="inputEmail"
          v-model="user.email"
          type="email"
          class="form-control"
          placeholder="Email address"
          required
          autofocus
        >
      </div>
      <div class="form-group">
        <label
          for="inputPassword"
          class="sr-only"
        >Password</label>
        <input
          id="inputPassword"
          v-model="user.password"
          type="password"
          class="form-control"
          placeholder="Password"
          required
        >
      </div>
      <button
        class="btn btn-lg btn-primary btn-block"
        type="submit"
      >
        登入
      </button>
      <p class="mt-5 mb-3 text-muted">
        &copy; 2020~∞ - 六角學院
      </p>
    </form>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data() {
    return {
      user: {
        email: '',
        password: '',
      },
      isLoading: false,
    };
  },
  methods: {
    signin() {
      this.isLoading = true;

      const api = `${process.env.VUE_APP_APIPATH}/api/auth/login`;

      this.$http.post(api, this.user).then((response) => {
        const token = response.data.token;
        const expired = response.data.expired;

        this.$cookies.set('token', token, new Date(expired * 1000));

        this.setAxiosAuthorization();

        this.$bus.$emit('message:push',
          '登入成功',
          'success');

        this.isLoading = false;

        this.$router.push('/admin/products');
      }).catch((error) => {
        this.$bus.$emit('message:push',
          `登入失敗惹，好糗Σ( ° △ °|||)︴
            ${error}`,
          'danger');

        this.isLoading = false;
      });
    },
  },
};
</script>

<style lang="scss" scoped>
html,
body {
  height: 100%;
  text-align: center;
}

body {
  display: flex;
  align-items: center;
  justify-content: center;
}

.form-signin {
  width: 100%;
  max-width: 330px;
  padding: 15px;
  margin: auto;
}
</style>
