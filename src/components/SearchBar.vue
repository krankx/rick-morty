<script setup>
import { ref } from 'vue'

const emit = defineEmits(['search'])
const query = ref('')

// Questions:
// 1. How many API requests were made while typing "morty"?
//    5, one for each letter (m, mo, mor, mort, morty).
// 2. What happens if a slow request from keystroke 2 arrives after keystroke 5?
//    It comes back last and overwrites the newer data, so the list shows results
//    for "mo" even though the input already says "morty".
// 3. How does this affect the server and the user experience?
//    The server gets a lot more requests than it needs, and the user can end up
//    looking at the wrong or outdated results.

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
