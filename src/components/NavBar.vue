<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-light">
    <div class="container-fluid">
      <a class="navbar-brand" href="#">Home</a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse"
      data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false"
      aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav">
          <li class="nav-item">
            <a class="nav-link active" aria-current="page"
            href="#" @click.prevent="goProduct">產品</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#"
            @click.prevent="this.$router.push('/dashboard/order')">訂單</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#"
            @click.prevent="this.$router.push('/dashboard/coupon')">優惠券</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#" @click.prevent="logout">登出</a>
          </li>
        </ul>
      </div>
    </div>
  </nav>
</template>

<script>
export default {
  methods: {
    logout() {
      const api = `${process.env.VUE_APP_API}logout`;
      this.$http.post(api)
        .then((res) => {
          console.log(res);
          this.$router.push('/login');
        });
    },
    goProduct() {
      this.$router.push('/dashboard/products');
    },
  },
};
</script>
