<script setup>
import { ref } from 'vue'

const emit = defineEmits(['search'])
const query = ref('')

// Questions:
// 1. How many API requests were made while typing "morty"?
//    5 requests — one per keystroke: m, mo, mor, mort, morty
// 2. What happens if a slow request from keystroke 2 arrives after keystroke 5?
//    The UI shows results for "mo" instead of "morty" — classic race condition
// 3. How does this affect the server and the user experience?
//    Server gets flooded with unnecessary requests; user can see stale/wrong results

const handleInput = () => {
  emit('search', query.value)
}
</script>

<template>
  <div class="search-bar">
    <input
      v-model="query"
      @input="handleInput"
      placeholder="Search characters..."
      type="text"
    />
  </div>
</template>

<style scoped>
.search-bar {
  margin-bottom: 20px;
}

.search-bar input {
  padding: 8px 14px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 8px;
  width: 260px;
  outline: none;
}

.search-bar input:focus {
  border-color: #42b883;
}
</style>
