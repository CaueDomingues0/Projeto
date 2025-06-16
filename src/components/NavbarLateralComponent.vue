<template>
  <!-- Navbar Horizontal -->
  <div class="fixed top-0 left-0 right-0 bg-white shadow-lg z-50">
    <!-- Linha principal da navbar -->
    <div class="flex items-center px-4 py-3">
      <!-- Barra de pesquisa (esquerda) -->
      <div class="w-80 relative">
        <input
          type="text"
          v-model="searchQuery"
          placeholder="Pesquise por produtos..."
          class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-600 focus:border-transparent text-sm"
        />
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-5 h-5 absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-400">
          <path stroke-linecap="round" stroke-linejoin="round" d="m21 21-5.197-5.197m0 0A7.5 7.5 0 1 0 5.196 5.196a7.5 7.5 0 0 0 10.607 10.607Z" />
        </svg>
        
        <!-- Resultados da pesquisa -->
        <div v-if="isSearching" class="absolute top-full left-0 right-0 bg-white border border-gray-300 rounded-md shadow-sm mt-1 p-2">
          <div class="text-xs text-gray-500">Buscando...</div>
        </div>
        <div v-if="searchResults.length > 0 && searchQuery.length > 0" class="absolute top-full left-0 right-0 bg-white border border-gray-300 rounded-md shadow-sm mt-1 max-h-60 overflow-y-auto z-50">
          <ul>
            <li v-for="product in searchResults" :key="product.id"
                class="px-4 py-2 hover:bg-gray-50 border-b border-gray-200 last:border-b-0">
              <router-link
                :to="{ name: 'ProdutoDetalhe', params: { id: product.id } }"
                @click="clearSearchAndClose"
                class="text-xs text-gray-700 hover:text-indigo-600 block truncate"
                :title="product.title"
              >
                {{ product.title }}
              </router-link>
            </li>
          </ul>
        </div>
        <div v-else-if="searchQuery.length > 0 && !isSearching && !searchError && searchAttempted" class="absolute top-full left-0 right-0 bg-white border border-gray-300 rounded-md shadow-sm mt-1 p-2">
          <div class="text-xs text-gray-500">Nenhum produto encontrado.</div>
        </div>
        <div v-if="searchError" class="absolute top-full left-0 right-0 bg-white border border-gray-300 rounded-md shadow-sm mt-1 p-2">
          <div class="text-xs text-red-500">{{ searchError }}</div>
        </div>
      </div>

      <!-- Nome da loja (centro) - Agora com posicionamento absoluto para centralização perfeita -->
      <div class="absolute left-1/2 transform -translate-x-1/2">
        <h2 class="text-2xl font-bold text-indigo-800 whitespace-nowrap">Domingues Loja</h2>
      </div>

      <!-- Ícones (direita) -->
      <div class="ml-auto flex items-center space-x-4">
        <!-- Ícone de usuário -->
        <button class="p-2 text-gray-600 hover:text-indigo-600 transition-colors duration-200">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M15.75 6a3.75 3.75 0 1 1-7.5 0 3.75 3.75 0 0 1 7.5 0ZM4.501 20.118a7.5 7.5 0 0 1 14.998 0A17.933 17.933 0 0 1 12 21.75c-2.676 0-5.216-.584-7.499-1.632Z" />
          </svg>
        </button>

        <!-- Ícone de carrinho -->
        <button class="p-2 text-gray-600 hover:text-indigo-600 transition-colors duration-200 relative">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M2.25 3h1.386c.51 0 .955.343 1.087.835l.383 1.437M7.5 14.25a3 3 0 0 0-3 3h15.75m-12.75-3h11.218c1.121-2.3 2.1-4.684 2.924-7.138a60.114 60.114 0 0 0-16.536-1.84M7.5 14.25L5.106 5.272M6 20.25a.75.75 0 1 1-1.5 0 .75.75 0 0 1 1.5 0Zm12.75 0a.75.75 0 1 1-1.5 0 .75.75 0 0 1 1.5 0Z" />
          </svg>
          <span class="absolute -top-1 -right-1 bg-red-500 text-white text-xs rounded-full h-5 w-5 flex items-center justify-center">0</span>
        </button>

        <!-- Ícone de configurações -->
        <button class="p-2 text-gray-600 hover:text-indigo-600 transition-colors duration-200">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M9.594 3.94c.09-.542.56-.94 1.11-.94h2.593c.55 0 1.02.398 1.11.94l.213 1.281c.063.374.313.686.645.87.074.04.147.083.22.127.325.196.72.257 1.075.124l1.217-.456a1.125 1.125 0 0 1 1.37.49l1.296 2.247a1.125 1.125 0 0 1-.26 1.431l-1.003.827c-.293.241-.438.613-.43.992a6.759 6.759 0 0 1 0 .255c-.008.378.137.75.43.991l1.004.827c.424.35.534.955.26 1.43l-1.298 2.247a1.125 1.125 0 0 1-1.369.491l-1.217-.456c-.355-.133-.75-.072-1.076.124a6.57 6.57 0 0 1-.22.128c-.331.183-.581.495-.644.869l-.213 1.281c-.09.543-.56.94-1.11.94h-2.594c-.55 0-1.019-.398-1.11-.94l-.213-1.281c-.062-.374-.312-.686-.644-.87a6.52 6.52 0 0 1-.22-.127c-.325-.196-.72-.257-1.076-.124l-1.217.456a1.125 1.125 0 0 1-1.369-.49l-1.297-2.247a1.125 1.125 0 0 1 .26-1.431l1.004-.827c.292-.24.437-.613.43-.991a6.932 6.932 0 0 1 0-.255c.007-.38-.138-.751-.43-.992l-1.004-.827a1.125 1.125 0 0 1-.26-1.43l1.297-2.247a1.125 1.125 0 0 1 1.37-.491l1.216.456c.356.133.751.072 1.076-.124.072-.044.146-.086.22-.128.332-.183.582-.495.644-.869l.214-1.28Z" />
            <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 1 1-6 0 3 3 0 0 1 6 0Z" />
          </svg>
        </button>
      </div>
    </div>

    <!-- Linha das categorias -->
    <div class="border-t border-gray-200 py-2">
      <nav class="flex justify-center items-center space-x-6 flex-wrap">
        <router-link to="/" @click="clearSearch" class="flex items-center space-x-2 px-3 py-2 rounded-lg hover:bg-gray-100 text-gray-700 hover:text-indigo-600 transition-colors duration-200">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-5 h-5">
            <path stroke-linecap="round" stroke-linejoin="round" d="M2.25 12l8.954-8.955c.44-.439 1.152-.439 1.591 0L21.75 12M4.5 9.75v10.125c0 .621.504 1.125 1.125 1.125H9.75v-4.875c0-.621.504-1.125 1.125-1.125h2.25c.621 0 1.125.504 1.125 1.125V21h4.125c.621 0 1.125-.504 1.125-1.125V9.75M8.25 21h8.25" />
          </svg>
          <span class="text-sm font-medium">Página Inicial</span>
        </router-link>
        
        <router-link to="/fragancia" @click="clearSearch" class="flex items-center space-x-2 px-3 py-2 rounded-lg hover:bg-gray-100 text-gray-700 hover:text-indigo-600 transition-colors duration-200">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-5 h-5">
            <path stroke-linecap="round" stroke-linejoin="round" d="M9.75 3.104v5.714a2.25 2.25 0 0 1-.659 1.591L5 14.5M9.75 3.104c-.251.023-.501.05-.75.082m.75-.082a24.301 24.301 0 0 1 4.5 0m0 0v5.714c0 .597.237 1.17.659 1.591L19.8 15.3M14.25 3.104c.251.023.501.05.75.082M19.8 15.3l-1.57.393A9.065 9.065 0 0 1 12 15a9.065 9.065 0 0 0-6.23-.693L5 14.5m14.8.8 1.402 1.402c1.232 1.232.65 3.318-1.067 3.611A48.309 48.309 0 0 1 12 21c-2.773 0-5.491-.235-8.135-.687-1.718-.293-2.3-2.379-1.067-3.61L5 14.5" />
          </svg>
          <span class="text-sm font-medium">Fragâncias</span>
        </router-link>

        <router-link to="/mobilia" @click="clearSearch" class="flex items-center space-x-2 px-3 py-2 rounded-lg hover:bg-gray-100 text-gray-700 hover:text-indigo-600 transition-colors duration-200">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-5 h-5">
            <path stroke-linecap="round" stroke-linejoin="round" d="M8.25 21v-4.875c0-.621.504-1.125 1.125-1.125h2.25c.621 0 1.125.504 1.125 1.125V21m0 0h4.5V9.375c0-.621.504-1.125 1.125-1.125H20.25M2.25 21h19.5m-18-18v18m10.5-18v4.875c0 .621.504 1.125 1.125 1.125h2.25c.621 0 1.125-.504 1.125-1.125V3m0 0h2.25A2.25 2.25 0 0 1 21 5.25v13.5A2.25 2.25 0 0 1 18.75 21H3a2.25 2.25 0 0 1-2.25-2.25V5.25A2.25 2.25 0 0 1 3 3h2.25" />
          </svg>
          <span class="text-sm font-medium">Mobílias</span>
        </router-link>

        <router-link to="/notebook" @click="clearSearch" class="flex items-center space-x-2 px-3 py-2 rounded-lg hover:bg-gray-100 text-gray-700 hover:text-indigo-600 transition-colors duration-200">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-5 h-5">
            <path stroke-linecap="round" stroke-linejoin="round" d="M9 17.25v1.007a3 3 0 0 1-.879 2.122L7.5 21h9l-.621-.621A3 3 0 0 1 15 18.257V17.25m6-12V15a2.25 2.25 0 0 1-2.25 2.25H5.25A2.25 2.25 0 0 1 3 15V5.25m18 0A2.25 2.25 0 0 0 18.75 3H5.25A2.25 2.25 0 0 0 3 5.25m18 0V12a2.25 2.25 0 0 1-2.25 2.25H5.25A2.25 2.25 0 0 1 3 12V5.25" />
          </svg>
          <span class="text-sm font-medium">Notebooks</span>
        </router-link>

        <router-link to="/moto" @click="clearSearch" class="flex items-center space-x-2 px-3 py-2 rounded-lg hover:bg-gray-100 text-gray-700 hover:text-indigo-600 transition-colors duration-200">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-5 h-5">
            <path stroke-linecap="round" stroke-linejoin="round" d="M8.25 18.75a1.5 1.5 0 0 1-3 0m3 0a1.5 1.5 0 0 0-3 0m3 0h6m-9 0H3.375a1.125 1.125 0 0 1-1.125-1.125V14.25m15.75 4.5a1.5 1.5 0 0 1-3 0m3 0a1.5 1.5 0 0 0-3 0m3 0h1.125A1.125 1.125 0 0 0 21 17.25v-4.5m-9-10.5h4.5m-4.5 0a1.5 1.5 0 0 0-1.5 1.5v1.5h3V6a1.5 1.5 0 0 0-1.5-1.5Z" />
          </svg>
          <span class="text-sm font-medium">Motos</span>
        </router-link>
      </nav>
    </div>
  </div>
</template>



<script setup>
import { ref, watch } from 'vue';
import axios from 'axios';

const searchQuery = ref('');
const searchResults = ref([]);
const isSearching = ref(false);
const searchError = ref(null);
const searchAttempted = ref(false);
let debounceTimer = null;

const performSearch = async () => {
  if (searchQuery.value.trim().length < 2) {
    searchResults.value = [];
    searchError.value = null;
    searchAttempted.value = false;
    return;
  }
  isSearching.value = true;
  searchError.value = null;
  searchAttempted.value = true;
  try {
    const response = await axios.get(`https://dummyjson.com/products/search?q=${searchQuery.value.trim()}`);
    searchResults.value = response.data.products;
  } catch (error) {
    console.error("Erro ao buscar produtos:", error);
    searchError.value = "Falha ao buscar.";
    searchResults.value = [];
  } finally {
    isSearching.value = false;
  }
};

watch(searchQuery, (newValue) => {
  clearTimeout(debounceTimer);
  searchAttempted.value = false;
  if (newValue.trim() === '') {
    searchResults.value = [];
    searchError.value = null;
    isSearching.value = false;
    return;
  }
  debounceTimer = setTimeout(performSearch, 500);
});

const clearSearch = () => {
  searchQuery.value = '';
  searchResults.value = [];
  searchError.value = null;
  searchAttempted.value = false;
};

const clearSearchAndClose = () => {
  clearSearch();
};
</script>
