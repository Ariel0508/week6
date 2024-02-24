<template>
    <h2>登入頁</h2>
    <div class="container d-flex justify-content-center align-items-center vh-100 w-100
    text-center">
      <div class="row">
        <form class="form-signin" @submit.prevent="login">
          <h1 class="h3 mb-3 fw-normal">請先登入</h1>

          <div class="form-floating mb-3">
            <input
              type="email"
              class="form-control"
              v-model="user.username"
              id="username"
              placeholder="name@example.com"
              required
              autofocus
            />
            <label for="username">Email address</label>
          </div>
          <div class="form-floating mb-3">
            <input
              type="password"
              class="form-control"
              v-model="user.password"
              id="password"
              placeholder="Password"
              required
            />
            <label for="password">Password</label>
          </div>
          <button class="w-100 btn btn-lg btn-primary" type="submit">登入</button>
          <p class="mt-5 mb-3 text-muted">&copy; 2024</p>
        </form>
      </div>
    </div>
  </template>

<script>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import axios from 'axios';

const { VITE_APP_URL } = import.meta.env;

export default {
  setup() {
    const user = ref({
      username: '',
      password: '',
    });
    const router = useRouter();

    const login = () => {
      axios
        .post(`${VITE_APP_URL}/admin/signin`, user.value)
        .then((res) => {
          // console.log(res);
          const { token, expired } = res.data;
          // console.log(token, expired);
          document.cookie = `rubbyToken=${token}; expires=${new Date(expired)};`;
          router.push('/admin/products');
        })
        .catch((err) => {
          // eslint-disable-next-line no-alert
          alert(err.response.data.message);
        });
    };
    return {
      user,
      login,
    };
  },
};
</script>
