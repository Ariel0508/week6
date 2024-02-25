<template>
    <div class="container py-5">
            <table class="table">
          <thead>
            <tr>
              <th width="150"></th>
              <th width="150" colspan="2">品名</th>
              <th width="150">數量/單位</th>
              <th width="120">單價</th>
              <th width="150" class="text-end">小計</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="cart in carts.carts" :key="cart.id">
              <td width="100">
                <button
                  type="button"
                  class="btn btn-outline-danger"
                  @click="removeCartItem(cart.id)"
                  :disabled="cart.productId === status.loadingItem"
                >
                X
                </button>
              </td>
              <td width="150">
                <div
                  style="
                    height: 150px;
                    background-size: cover;
                    background-position: center;
                  "
                  :style="{ backgroundImage: `url(${cart.product.imageUrl})` }"
                ></div>
              </td>
              <td width="150">{{ cart.product.title }}</td>
              <td width="120">
                <div class="input-group">
                  <button
                    v-if="cart.qty > 1"
                    type="button"
                    class="btn btn-outline-secondary"
                    :disabled="cart.qty === 1"
                    @click="
                      cart.qty--;
                      changeCartQty(cart, cart.qty);
                    "
                  >
                    -
                  </button>
                  <button
                    v-else
                    type="button"
                    class="btn btn-outline-danger"
                    @click="removeCartItem(cart.id)"
                  >
                  -
                  </button>
                  <input
                    v-model="cart.qty"
                    type="number"
                    min="0"
                    max="20"
                    class="form-control text-center"
                    aria-label="Dollar amount (with dot and two decimal places)"
                    readonly
                    :disabled="cart.productId === status.loadingItem"
                  />
                  <button
                    type="button"
                    class="btn btn-outline-secondary"
                    @click="
                      cart.qty++;
                      changeCartQty(cart, cart.qty);
                    "
                  >
                    +
                  </button>
                </div>
              </td>
              <td width="120">{{ cart.product.price }}</td>
              <td width="150" class="text-end">{{ cart.total }}</td>
            </tr>
          </tbody>
          <tfoot>
            <tr>
                <td colspan="5" class="border-0">
                <button
                  type="button"
                  class="btn btn-outline-danger"
                  @click="removeCartAllItem()"
                >
                  清空購物車
                </button>
              </td>
              <td colspan="6" class="border-0 text-end">
                <span class="text-end fw-bold text-danger fs-5">總計:{{ carts.final_total }}元</span>
              </td>
            </tr>
          </tfoot>
        </table>
     <!-- form -->
     <div class="my-5 row justify-content-center">
          <v-form
            ref="formRef"
            class="col-md-6 form-floating"
            v-slot="{ errors }"
            @submit="createOrder"
          >
            <div class="mb-3">
              <label for="email" class="form-label">Email</label>
              <v-field
                id="email"
                name="email"
                type="email"
                class="form-control"
                :class="{ 'is-invalid': errors['email'] }"
                placeholder="請輸入 Email"
                rules="email|required"
                v-model="form.user.email"
              ></v-field>
              <error-message
                name="email"
                class="invalid-feedback"
              ></error-message>
            </div>

            <div class="mb-3">
              <label for="name" class="form-label">收件人姓名</label>
              <v-field
                id="name"
                name="name"
                type="text"
                class="form-control"
                :class="{ 'is-invalid': errors['name'] }"
                placeholder="請輸入姓名"
                rules="required"
                v-model="form.user.name"
              ></v-field>
              <error-message
                name="name"
                class="invalid-feedback"
              ></error-message>
            </div>

            <div class="mb-3">
              <label for="tel" class="form-label">收件人電話</label>
              <v-field
                id="tel"
                name="tel"
                type="text"
                class="form-control"
                :class="{ 'is-invalid': errors['tel'] }"
                placeholder="請輸入電話"
                :rules="isPhone"
                v-model="form.user.tel"
              ></v-field>
              <error-message
                name="tel"
                class="invalid-feedback"
              ></error-message>
            </div>

            <div class="mb-3">
              <label for="address" class="form-label">收件人地址</label>
              <v-field
                id="address"
                name="address"
                type="text"
                class="form-control"
                :class="{ 'is-invalid': errors['address'] }"
                placeholder="請輸入地址"
                rules="required"
                v-model="form.user.address"
              ></v-field>
              <error-message
                name="address"
                class="invalid-feedback"
              ></error-message>
            </div>

            <div class="mb-3">
              <label for="message" class="form-label">留言</label>
              <textarea
                name=""
                id="message"
                class="form-control"
                cols="30"
                rows="10"
                v-model="form.message"
              ></textarea>
            </div>
            <div class="text-end">
              <button
                type="submit"
                class="btn btn-danger"
                :disabled="carts.total === 0"
              >
                送出訂單
              </button>
            </div>
          </v-form>
        </div>
    </div>
  </template>

<script>
import { onMounted, ref, watch } from 'vue';
import axios from 'axios';

const { VITE_APP_URL } = import.meta.env;
const { VITE_APP_PATH } = import.meta.env;

export default {
  props: ['tempProduct', 'addToCart'],
  setup(props) {
    const Q = ref(1);
    const carts = ref({});
    const status = ref({
      loadingItem: '',
    });
    const formRef = ref(null);
    const form = ref({
      user: {
        name: '',
        email: '',
        tel: '',
        address: '',
      },
      message: '',
    });
    const getCart = () => {
      const url = `${VITE_APP_URL}/api/${VITE_APP_PATH}/cart`;
      axios.get(url).then((res) => {
        carts.value = res.data.data;
      });
    };
    // 表單驗證
    const createOrder = () => {
      const url = `${VITE_APP_URL}/api/${VITE_APP_PATH}/order`;
      const order = form.value;
      axios.post(url, { data: order }).then((response) => {
        // eslint-disable-next-line no-alert
        alert(response.data.message);
        if (formRef.value) {
          formRef.value.resetForm();
        }
        getCart();
      }).catch((err) => {
        // eslint-disable-next-line no-alert
        alert(err.response.data.message);
      });
    };
    const isPhone = (value) => {
      const phoneNumber = /^(09)[0-9]{8}$/;
      return phoneNumber.test(value) ? true : '需要正確的電話號碼';
    };
    const changeCartQty = (item, qty = 1) => {
      const order = {
        product_id: item.product_id,
        qty,
      };
      status.value.loadingItem = item.id;
      const url = `${VITE_APP_URL}/api/${VITE_APP_PATH}/cart/${item.id}`;
      axios.put(url, { data: order }).then(() => {
        status.value.loadingItem = '';
        // alert(res.data.message);
        getCart();
      });
    };
    const removeCartItem = (id) => {
      status.value.loadingItem = id;
      const url = `${VITE_APP_URL}/api/${VITE_APP_PATH}/cart/${id}`;
      axios.delete(url).then((res) => {
        status.value.loadingItem = '';
        // eslint-disable-next-line no-alert
        alert(res.data.message);
        getCart();
      });
    };
    const removeCartAllItem = () => {
      const url = `${VITE_APP_URL}/api/${VITE_APP_PATH}/carts`;
      axios.delete(url).then(() => {
        // eslint-disable-next-line no-alert
        alert('確定要清空購物車?');
        getCart();
      });
    };
    onMounted(() => {
      getCart();
    });
    watch(
      () => props.tempProduct,
      () => {
        Q.value = 1;
      },
    );
    return {
      Q,
      status,
      carts,
      form,
      createOrder,
      isPhone,
      changeCartQty,
      removeCartItem,
      removeCartAllItem,
    };
  },
};

</script>
