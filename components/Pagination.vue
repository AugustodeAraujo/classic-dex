<template>
  <nav aria-label="Page navigation">
    <ul class="pagination">
      <li
        class="page-item"
        :class="{ disabled: modelValue === 1 }"
        @click.prevent="changePage(modelValue - 1)"
      >
        <a class="page-link text-rose-600" href="#">
          <Icon name="material-symbols:arrow-back-ios-rounded" size="12" />
        </a>
      </li>
      <li v-if="startPage > 1" class="page-item" @click.prevent="changePage(1)">
        <a class="page-link" href="#">1</a>
      </li>
      <li v-if="startPage > 2" class="page-item disabled">
        <a class="page-link" href="#">...</a>
      </li>
      <li
        v-for="page in pagesToShow"
        :key="page"
        class="page-item"
        :class="{ active: modelValue === page }"
        @click.prevent="changePage(page)"
      >
        <a class="page-link" href="#">{{ page }}</a>
      </li>
      <li v-if="endPage < totalPages" class="page-item disabled">
        <a class="page-link" href="#">...</a>
      </li>
      <li
        v-if="endPage < totalPages"
        class="page-item"
        @click.prevent="changePage(totalPages)"
      >
        <a class="page-link" href="#">{{ totalPages }}</a>
      </li>
      <li
        class="page-item"
        :class="{ disabled: modelValue === totalPages }"
        @click.prevent="changePage(modelValue + 1)"
      >
        <a class="page-link" href="#">
          <Icon name="material-symbols:arrow-forward-ios-rounded" size="12" />
        </a>
      </li>
    </ul>
  </nav>
</template>

<script setup>
import { computed } from "vue";

const props = defineProps({
  totalItems: {
    type: Number,
    required: true,
  },
  itemsPerPage: {
    type: Number,
    default: 10,
  },
  modelValue: {
    type: Number,
    required: true,
  },
});

const emit = defineEmits(["update"]);

const totalPages = computed(() => {
  return Math.ceil(props.totalItems / props.itemsPerPage);
});

const startPage = computed(() => {
  if (props.modelValue <= 3) return 1;
  if (props.modelValue >= totalPages.value - 2) return totalPages.value - 4;
  return props.modelValue - 2;
});

const endPage = computed(() => {
  return Math.min(startPage.value + 4, totalPages.value);
});

const pagesToShow = computed(() => {
  const pages = [];
  for (let i = startPage.value; i <= endPage.value; i++) {
    pages.push(i);
  }
  return pages;
});

const changePage = (page) => {
  if (page > 0 && page <= totalPages.value) {
    emit("update", page);
  }
};
</script>

<style scoped>
.pagination {
  display: flex;
  list-style: none;
  padding: 0;
}

.page-item {
  margin: 0 5px;
}

.page-item a {
  text-decoration: none;
  padding: 8px 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
  color: #e11d48;
  cursor: pointer;
}

.page-item.disabled a {
  color: #6c757d;
  cursor: not-allowed;
}

.page-item.active a {
  background-color: #e11d48;
  color: white;
}
</style>
