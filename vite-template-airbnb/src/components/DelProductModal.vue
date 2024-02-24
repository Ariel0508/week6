<template>
    <div
      class="modal"
      tabindex="-1"
      id="delProductModal"
      ref="delProductModalRef"
      aria-labelledby="delProductModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header bg-danger text-white">
            <h5 class="modal-title">刪除商品</h5>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <p>
              是否刪除
              <strong class="text-danger">{{ item.title }}</strong>
              商品(刪除後將無法恢復)。
            </p>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-outline-secondary"
              data-bs-dismiss="modal"
            >
              取消
            </button>
            <button type="button" class="btn btn-danger" @click="delProduct">
              確定刪除
            </button>
          </div>
        </div>
      </div>
    </div>
  </template>

<script>
import { ref, onMounted } from 'vue';
import { Modal } from 'bootstrap';
import axios from 'axios';

const { VITE_APP_URL } = import.meta.env;
const { VITE_APP_PATH } = import.meta.env;
export default {
  props: ['item'],
  setup(props, { emit }) {
    const delProductModal = ref(null);
    const delProductModalRef = ref(null);
    const delProduct = () => {
      axios
        .delete(`${VITE_APP_URL}/api/${VITE_APP_PATH}/admin/product/${props.item.id}`)
        .then(() => {
          // 獲取最新資料
          emit('update');
          // 隱藏modal
          delProductModal.value.hide();
        })
        .catch((err) => {
          // eslint-disable-next-line no-alert
          alert(err.response.data.message);
        });
    };
    const oModal = () => {
      delProductModal.value.show();
    };
    const cModal = () => {
      delProductModal.value.hide();
    };
    onMounted(() => {
      // delete modal
      delProductModal.value = new Modal(delProductModalRef.value, {
        keyboard: false,
        backdrop: 'static',
      });
    });

    return {
      delProduct,
      oModal,
      cModal,
      delProductModalRef,
    };
  },
};
</script>
