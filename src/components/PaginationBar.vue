<script setup>
import { computed } from 'vue'

const props = defineProps({
  currentPage: Number,
  totalPages: Number
})

const emit = defineEmits(['page-change'])

const pages = computed(() => {
  const total = props.totalPages
  const current = props.currentPage
  const result = []

  if (total <= 7) {
    for (let i = 1; i <= total; i++) {
      result.push(i)
    }
    return result
  }

  result.push(1)

  if (current > 3) {
    result.push('...')
  }

  for (let i = current - 1; i <= current + 1; i++) {
    if (i > 1 && i < total) {
      result.push(i)
    }
  }

  if (current < total - 2) {
    result.push('...')
  }

  result.push(total)

  return result
})

const clickPage = (p) => {
  if (p === '...') return
  emit('page-change', p)
}
</script>

<template>
  <div class="pagination">
    <button
      :disabled="currentPage === 1"
      @click="clickPage(currentPage - 1)"
    >
      Prev
    </button>

    <button
      v-for="(p, i) in pages"
      :key="i"
      @click="clickPage(p)"
      :class="{ active: p === currentPage }"
    >
      {{ p }}
    </button>

    <button
      :disabled="currentPage === totalPages"
      @click="clickPage(currentPage + 1)"
    >
      Next
    </button>
  </div>
</template>

<style>
.pagination {
  display: flex;
  gap: 6px;
  margin-top: 20px;
  flex-wrap: wrap;
}

button.active {
  background: black;
  color: white;
}
</style>