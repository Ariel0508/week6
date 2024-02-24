<template>
    <div
      class="modal"
      tabindex="-1"
      id="productModal"
      ref="pModalRef"
      aria-labelledby="productModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-xl">
        <div class="modal-content">
          <div class="modal-header bg-dark text-white">
            <h5 class="modal-title">
              <span v-if="status">新增產品</span>
              <span v-else>編輯產品</span>
            </h5>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <div class="row">
              <!-- 圖片 -->
              <div class="col-md-4">
                <div class="row">
                  <div class="mb-3">
                    <label for="imageUrl" class="col-form-label"
                      >主要圖片</label
                    >
                    <input
                      type="text"
                      class="form-control mb-3"
                      id="imageUrl"
                      v-model="editProduct.imageUrl"
                    />
                    <img :src="editProduct.imageUrl" class="w-100" />
                  </div>
                </div>
                <!-- 上傳圖片 -->
                <h3>多圖新增</h3>
                <div class="mb-3" v-if="Array.isArray(editProduct.imagesUrl)">
                  <div
                    class="mb-3"
                    v-for="(img, key) in editProduct.imagesUrl"
                    :key="key"
                  >
                    <label for="imagesUrl" class="col-form-label"
                      >圖片網址</label
                    >
                    <input
                      type="text"
                      class="form-control mb-3"
                      id="imagesUrl"
                      v-model="editProduct.imagesUrl[key]"
                    />
                    <img :src="img" class="w-100" />
                  </div>
                  <!-- Product.imagesUrl不為空陣列或最後一個元素不是空字串 -->
                  <div
                    v-if="!editProduct.imagesUrl.length ||
                  editProduct.imagesUrl[editProduct.imagesUrl.length - 1]"
                  >
                    <button
                      type="button"
                      class="btn btn-sm btn-outline-primary d-block w-100"
                      @click="editProduct.imagesUrl.push('')"
                    >
                      新增圖片
                    </button>
                  </div>
                  <div v-else>
                    <button
                      type="button"
                      class="btn btn-sm btn-outline-danger d-block w-100"
                      @click="editProduct.imagesUrl.pop()"
                    >
                      刪除圖片
                    </button>
                  </div>
                </div>
                <div v-else>
                  <button
                    type="button"
                    class="btn btn-sm btn-outline-primary d-block w-100"
                    @click="createImages"
                  >
                    新增圖片
                  </button>
                </div>
                <div class="mb-3"></div>
              </div>
              <!-- 標題內容 -->
              <div class="col-md-8">
                <div class="row">
                  <div class="mb-3">
                    <label for="title" class="col-form-label">標題</label>
                    <input
                      type="text"
                      class="form-control"
                      id="title"
                      v-model="editProduct.title"
                    />
                  </div>
                </div>
                <div class="row">
                  <div class="col-md-6 mb-3">
                    <label for="category" class="col-form-label">分類</label>
                    <input
                      type="text"
                      class="form-control"
                      id="category"
                      v-model="editProduct.category"
                    />
                  </div>
                  <div class="col-md-6 mb-3">
                    <label for="unit" class="col-form-label">單位</label>
                    <input
                      type="text"
                      class="form-control"
                      id="unit"
                      v-model="editProduct.unit"
                    />
                  </div>
                </div>
                <div class="row">
                  <div class="col-md-6 mb-3">
                    <label for="origin_price" class="col-form-label"
                      >原價</label
                    >
                    <input
                      type="number"
                      class="form-control"
                      id="origin_price"
                      v-model.number="editProduct.origin_price"
                    />
                  </div>
                  <div class="col-md-6 mb-3">
                    <label for="price" class="col-form-label">售價</label>
                    <input
                      type="number"
                      class="form-control"
                      id="price"
                      v-model.number="editProduct.price"
                    />
                  </div>
                </div>
                <div class="row mb-3">
                  <label for="description" class="col-form-label"
                    >商品描述</label
                  >
                  <textarea
                    class="form-control"
                    id="description"
                    v-model="editProduct.description"
                  ></textarea>
                </div>
                <div class="row mb-3">
                  <label for="content" class="col-form-label">商品內容</label>
                  <textarea
                    class="form-control"
                    id="content"
                    v-model="editProduct.content"
                  ></textarea>
                </div>
                <div class="form-check">
                  <input
                    id="is_enabled"
                    v-model="editProduct.is_enabled"
                    class="form-check-input"
                    type="checkbox"
                    :true-value="1"
                    :false-value="0"
                  />
                  <label class="form-check-label" for="is_enabled"
                    >是否啟用</label
                  >
                </div>
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-outline-secondary"
              data-bs-dismiss="modal"
            >
              取消
            </button>
            <button
              type="button"
              class="btn btn-primary"
              @click="updateProduct"
            >
              確定
            </button>
          </div>
        </div>
      </div>
    </div>
    </template>

<script>
import { ref, onMounted, watch } from 'vue';
import { Modal } from 'bootstrap';
import axios from 'axios';

const { VITE_APP_URL } = import.meta.env;
const { VITE_APP_PATH } = import.meta.env;
export default {
  props: ['status', 'product', 'updateProduct'],
  setup(props, { emit }) {
    const pModal = ref(null);
    const pModalRef = ref(null);
    const editProduct = ref({});

    const oModal = () => {
      pModal.value.show();
    };

    const cModal = () => {
      pModal.value.hide();
    };
    const updateProduct = () => {
      // 新增
      let productUrl = `${VITE_APP_URL}/api/${VITE_APP_PATH}/admin/product`;
      let http = 'post';
      // 編輯
      if (!props.status) {
        productUrl = `${VITE_APP_URL}/api/${VITE_APP_PATH}/admin/product/${props.product.id}`;
        http = 'put';
      }
      axios[http](productUrl, { data: props.product })
        .then((res) => {
          // eslint-disable-next-line no-alert
          alert(res.data.message);
          // 獲取最新資料
          emit('update');
          // 隱藏modal
          cModal();
        })
        .catch((err) => {
          // eslint-disable-next-line no-alert
          alert(err.response.data.message);
        });
    };

    // 產品圖片
    const createImages = () => {
      editProduct.value.imagesUrl = [];
      editProduct.value.imagesUrl.push('');
    };
      // const createImages = (product) => {
      //   const newProduct = { ...product };
      //   newProduct.imagesUrl = [...newProduct.imagesUrl, ''];
      //   return newProduct;
      // };
    watch(
      () => props.product,
      () => {
        editProduct.value = props.product;
      },
    );
    onMounted(() => {
      pModal.value = new Modal(pModalRef.value, {
        keyboard: false,
        backdrop: 'static',
      });
      editProduct.value = props.product;
    });

    return {
      // eslint-disable-next-line vue/no-dupe-keys
      updateProduct,
      createImages,
      oModal,
      cModal,
      pModalRef,
      editProduct,
    };
  },
};
</script>
