<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'
import PaginationBar from './components/PaginationBar.vue'
import SearchBar from './components/SearchBar.vue'

const characters = ref([])
const currentPage = ref(1)
const totalPages = ref(1)
const loading = ref(false)
const searchQuery = ref('')
const noResults = ref(false)

let abortController = null

function debounce(fn, delay) {
  let timer = null
  return function (...args) {
    clearTimeout(timer)
    timer = setTimeout(() => fn(...args), delay)
  }
}

const fetchCharacters = async (page, query = '') => {
  if (abortController) {
    abortController.abort()
  }
  abortController = new AbortController()

  try {
    loading.value = true
    noResults.value = false

    let url = `https://rickandmortyapi.com/api/character?page=${page}`
    if (query) url += `&name=${encodeURIComponent(query)}`

    const res = await axios.get(url, { signal: abortController.signal })

    characters.value = res.data.results
    totalPages.value = res.data.info.pages
    currentPage.value = page

    window.history.replaceState({}, '', `?page=${page}`)
  } catch (e) {
    if (axios.isCancel(e)) return
    if (e.response?.status === 404) {
      characters.value = []
      totalPages.value = 1
      noResults.value = true
    } else {
      console.log('API error:', e)
    }
  } finally {
    loading.value = false
  }
}

const debouncedSearch = debounce((query) => {
  fetchCharacters(1, query)
}, 400)

const handleSearch = (query) => {
  searchQuery.value = query
  debouncedSearch(query)
}

const changePage = (page) => {
  page = Number(page)

  if (page < 1 || page > totalPages.value) return
  if (page === currentPage.value) return

  fetchCharacters(page, searchQuery.value)

  window.scrollTo({ top: 0, behavior: 'smooth' })
}

onMounted(() => {
  const urlPage =
    Number(new URLSearchParams(window.location.search).get('page')) || 1

  fetchCharacters(urlPage)
})
</script>

<template>
  <div>
    <h1>Rick & Morty</h1>

    <SearchBar @search="handleSearch" />

    <div v-if="loading">Loading...</div>

    <div v-else-if="noResults" class="no-results">
      No characters found
    </div>

    <div v-else class="list">
      <div v-for="c in characters" :key="c.id" class="card">
        <img :src="c.image" width="100" />
        <p>{{ c.name }}</p>
      </div>
    </div>

    <PaginationBar
      :currentPage="currentPage"
      :totalPages="totalPages"
      @page-change="changePage"
    />
  </div>
</template>

<style>
.list {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  margin-top: 20px;
}

.card {
  width: 140px;
  padding: 10px;
  border-radius: 12px;
  background: #fff;

  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.12);

  transition: transform 0.2s ease, box-shadow 0.2s ease;
  text-align: center;
}

.card:hover {
  transform: translateY(-4px);

  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.18);
}

img {
  border-radius: 10px;
}

.no-results {
  margin-top: 40px;
  font-size: 18px;
  color: #999;
}
</style>
