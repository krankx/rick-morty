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

  const range = []

  const add = (p) => {
    if (!range.includes(p)) range.push(p)
  }

  add(1)

  for (let i = current - 1; i <= current + 1; i++) {
    if (i > 1 && i < total) add(i)
  }

  add(total)

  range.sort((a, b) => a - b)

  const result = []

  for (let i = 0; i < range.length; i++) {
    result.push(range[i])

    if (range[i + 1] && range[i + 1] - range[i] > 1) {
      result.push('...')
    }
  }

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