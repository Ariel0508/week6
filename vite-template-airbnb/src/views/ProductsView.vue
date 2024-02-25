<template>
  <div id="app">
    <div class="container">
      <div class="row m-3">
        <table class="table">
          <thead>
            <tr>
              <th width="150">圖片</th>
              <th width="150">產品名稱</th>
              <th width="120">價格</th>
              <th width="150"></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="product in products" :key="product.id">
              <td width="200">
                <div
                  style="
                    height: 250px;
                    background-size: cover;
                    background-position: center;
                  "
                  :style="{ backgroundImage: `url(${product.imageUrl})` }"
                ></div>
              </td>
              <td width="150">{{ product.title }}</td>
              <td width="120">
                <div v-if="product.price === product.origin_price">
                  {{ product.origin_price }}元
                </div>
                <div v-else>
                  <del>原價{{ product.origin_price }}元</del>
                  <div class="text-danger">現在只要{{ product.price }}元</div>
                </div>
              </td>
              <td width="200">
                <button
                  type="button"
                  class="btn btn-outline-secondary"
                  @click="openModal(product)"
                >
                  查看更多
                </button>
                <button
                  type="button"
                  class="btn btn-outline-danger"
                  :disable="product.id === status.loadingItem"
                  @click="addToCart(product.id, 1)"
                >
                  <span
                    v-if="product.id === status.loadingItem"
                    class="spinner-border spinner-border-sm"
                    aria-hidden="true"
                  ></span>
                  加入購物車
                </button>
              </td>
            </tr>
          </tbody>
          <tfoot>
            <tr>
                <td colspan="5" class="border-0 text-end">
                <button
                  type="button"
                  class="btn btn-primary"
                  @click="openModal()"
                >
                  購物車
                </button>
              </td>
            </tr>
          </tfoot>
        </table>
    </div>
  </div>
  </div>
</template>

<script>
import axios from 'axios';
import { onMounted, ref } from 'vue';
import { useRouter, useRoute } from 'vue-router';

const { VITE_APP_URL } = import.meta.env;
const { VITE_APP_PATH } = import.meta.env;
export default {
  setup() {
    const router = useRouter();
    const route = useRoute();
    const id = ref(route.params.productId);
    const products = ref([]);
    const tempProduct = ref({});
    const status = ref({
      loadingItem: '',
    });
    const getProducts = () => {
      const url = `${VITE_APP_URL}/api/${VITE_APP_PATH}/products/all`;
      axios.get(url).then((res) => {
        products.value = res.data.products;
        // console.log(res.data.products);
      });
    };
    const addToCart = (productId, qty = 1) => {
      status.value.loadingItem = productId;
      const order = {
        product_id: productId,
        qty,
      };
      const url = `${VITE_APP_URL}/api/${VITE_APP_PATH}/cart`;
      axios.post(url, { data: order }).then((res) => {
        status.value.loadingItem = '';
        // eslint-disable-next-line no-alert
        alert(res.data.message);
        // userModal.value.close();
      });
    };
    const openModal = () => {
      router.push('/cart');
    };
    onMounted(() => {
      getProducts();
    });
    return {
      id,
      products,
      tempProduct,
      status,
      getProducts,
      addToCart,
      openModal,
    };
  },
  components: {
    // userProductModal,
  },
};
</script>
