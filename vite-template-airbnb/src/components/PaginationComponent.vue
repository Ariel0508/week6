<template>
    <nav aria-label="Page navigation example">
      <ul class="pagination">
        <!-- 當前頁面是第一頁時，添加 "disabled" class -->
        <li class="page-item" :class="{ disabled: pages.current_page === 1 }">
          <!-- 設置了 class 和點擊事件監聽器，用於觸發上一頁的操作 -->
          <a
            class="page-link"
            aria-label="Previous"
            @click.prevent="emitPages(pages.current_page - 1)"
          >
            <span aria-hidden="true">&laquo;</span>
          </a>
        </li>
        <!-- 使用了 v-for 指令來循環生成分頁按鈕，並使用 v-bind 指令來動態設置 class，當分頁按鈕是當前頁面時，添加 "active" class。 -->
        <li
          v-for="(item, index) in pages.total_pages"
          :key="index"
          class="page-item"
          :class="{ active: item === pages.current_page }"
        >
          <!-- 使用了 v-if 指令，當分頁按鈕是當前頁面時，顯示純文字內容 -->
          <span class="page-link" v-if="item === pages.current_page">{{
            item
          }}</span>
          <!--  v-else 指令，當分頁按鈕不是當前頁面時，顯示可點擊的連結，並設置點擊事件監聽器 -->
          <a class="page-link" v-else @click.prevent="emitPages(item)">{{
            item
          }}</a>
        </li>
        <!-- 顯示下一頁的按鈕和觸發下一頁的操作 -->
        <li
          class="page-item"
          :class="{ disabled: pages.current_page === pages.total_pages }"
        >
          <a
            class="page-link"
            aria-label="Next"
            @click.prevent="emitPages(pages.current_page + 1)"
          >
            <span aria-hidden="true">&raquo;</span>
          </a>
        </li>
      </ul>
    </nav>
  </template>

<script>
export default {
  props: ['pages'],
  setup(props, { emit }) {
    const emitPages = (item) => {
      emit('emit-pages', item);
    };

    return {
      emitPages,
    };
  },
};
</script>
