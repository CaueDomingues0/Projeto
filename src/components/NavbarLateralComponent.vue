<template>
  <div class="bg-gray-100 shadow-lg p-4 w-full">
    <!-- Barra de Navegação (Navbar) com Barra de Pesquisa, Título Centralizado e Ícones à Direita -->
    <div class="flex items-center justify-between w-full">
      
      <!-- Barra de Pesquisa no Canto Esquerdo -->
      <div class="flex items-center">
        <input
          type="text"
          v-model="searchQuery"
          placeholder="Pesquise por produtos..."
          class="w-80 px-4 py-2 border border-black rounded-none focus:outline-none focus:ring-1 focus:ring-black focus:border-black text-sm placeholder-gray-400"
        />
      </div>

      <!-- Título "Domingues loja" Centralizado -->
      <h2 class="text-2xl font-bold text-red-500 mx-auto">Domingues loja</h2>

      <!-- Ícones à Direita -->
      <div class="flex items-center space-x-4">
        <!-- Ícone de Usuário -->
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="size-6">
          <path fill-rule="evenodd" d="M7.5 6a4.5 4.5 0 1 1 9 0 4.5 4.5 0 0 1-9 0ZM3.751 20.105a8.25 8.25 0 0 1 16.498 0 .75.75 0 0 1-.437.695A18.683 18.683 0 0 1 12 22.5c-2.786 0-5.433-.608-7.812-1.7a.75.75 0 0 1-.437-.695Z" clip-rule="evenodd" />
        </svg>


        <!-- Ícone de Carrinho de Compras -->
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="size-6">
          <path d="M2.25 2.25a.75.75 0 0 0 0 1.5h1.386c.17 0 .318.114.362.278l2.558 9.592a3.752 3.752 0 0 0-2.806 3.63c0 .414.336.75.75.75h15.75a.75.75 0 0 0 0-1.5H5.378A2.25 2.25 0 0 1 7.5 15h11.218a.75.75 0 0 0 .674-.421 60.358 60.358 0 0 0 2.96-7.228.75.75 0 0 0-.525-.965A60.864 60.864 0 0 0 5.68 4.509l-.232-.867A1.875 1.875 0 0 0 3.636 2.25H2.25ZM3.75 20.25a1.5 1.5 0 1 1 3 0 1.5 1.5 0 0 1-3 0ZM16.5 20.25a1.5 1.5 0 1 1 3 0 1.5 1.5 0 0 1-3 0Z" />
        </svg>


        <!-- Ícone de Configurações -->
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="size-6">
          <path d="M18.75 12.75h1.5a.75.75 0 0 0 0-1.5h-1.5a.75.75 0 0 0 0 1.5ZM12 6a.75.75 0 0 1 .75-.75h7.5a.75.75 0 0 1 0 1.5h-7.5A.75.75 0 0 1 12 6ZM12 18a.75.75 0 0 1 .75-.75h7.5a.75.75 0 0 1 0 1.5h-7.5A.75.75 0 0 1 12 18ZM3.75 6.75h1.5a.75.75 0 1 0 0-1.5h-1.5a.75.75 0 0 0 0 1.5ZM5.25 18.75h-1.5a.75.75 0 0 1 0-1.5h1.5a.75.75 0 0 1 0 1.5ZM3 12a.75.75 0 0 1 .75-.75h7.5a.75.75 0 0 1 0 1.5h-7.5A.75.75 0 0 1 3 12ZM9 3.75a2.25 2.25 0 1 0 0 4.5 2.25 2.25 0 0 0 0-4.5ZM12.75 12a2.25 2.25 0 1 1 4.5 0 2.25 2.25 0 0 1-4.5 0ZM9 15.75a2.25 2.25 0 1 0 0 4.5 2.25 2.25 0 0 0 0-4.5Z" />
        </svg>

      </div>
    </div>

    <!-- Exibindo o status "Buscando..." -->
    <div v-if="isSearching" class="text-xs text-gray-500 mt-2 px-1">Buscando...</div>

    <!-- Exibindo resultados da pesquisa -->
    <div v-if="searchResults.length > 0 && searchQuery.length > 0" class="mt-2 bg-white border border-black rounded-md shadow-md max-h-60 overflow-y-auto w-full">
      <ul>
        <li v-for="product in searchResults" :key="product.id"
            class="px-4 py-2 hover:bg-indigo-100 border-b border-gray-200 last:border-b-0">
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

    <!-- Caso não haja resultados -->
    <div v-else-if="searchQuery.length > 0 && !isSearching && !searchError && searchAttempted" class="text-xs text-gray-500 mt-2 px-1">
      Nenhum produto encontrado.
    </div>

    <!-- Exibindo erro -->
    <div v-if="searchError" class="text-xs text-red-500 mt-2 px-1">
      {{ searchError }}
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
