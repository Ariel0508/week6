<template>
    <h2>這是後台</h2>
    <nav>
    <RouterLink to="/admin/products">產品列表</RouterLink>|
    <RouterLink to="/admin/order">訂單列表</RouterLink>|
    <RouterLink to="/">回到前台</RouterLink>
    </nav>
    <RouterView />
</template>

<script>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';

const { VITE_APP_URL } = import.meta.env;

export default {
  setup() {
    const products = ref([]);
    const tempProduct = ref({});
    const router = useRouter();

    // 1.確認是否登入
    const checkLogin = () => {
      axios
        .post(`${VITE_APP_URL}/api/user/check`)
        .then(() => {
        //   console.log('驗證成功', res.data.success);
        })
        .catch((err) => {
          // eslint-disable-next-line no-alert
          alert(err.response.data.message);
          router.push('/login');
        });
    };

    onMounted(() => {
      // Retrieve Token
      const token = document.cookie.replace(/(?:(?:^|.*;\s*)rubbyToken\s*=\s*([^;]*).*$)|^.*$/, '$1');
      axios.defaults.headers.common.Authorization = token;
      checkLogin();
    });

    return {
      products,
      tempProduct,
    };
  },
};
</script>
