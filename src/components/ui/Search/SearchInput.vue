<script setup>
import { getSearchQuotes } from "../../../apis/getSearchQuotes"
import { ref, defineEmits } from "vue";

function debounce(fn , delay) {
    let timeout = null;
    
    return function() {
      clearTimeout(timeout);
      timeout = setTimeout(() => {
        fn.apply(this, arguments)
      }, delay)
    }
  }

  const input = ref("");
  const quotes = ref(null);

  const emit = defineEmits(["on-search-start", "on-search-end"]);

  const onSearchInput = debounce(async function() {
    if(input.value) {
      emit("on-search-start");
      try {
         const data = await getSearchQuotes(input.value);
         emit("on-search-end",data.results);
      } catch(e) {
        console.error(e);
        emit("on-search-end");
      }
    }
  },300);
</script>

<template>
  <form 
    class="w-17" 
    @submit="onSearchInput"
  >   
    <label for="default-search" class="mb-2 text-sm font-medium text-gray-900 sr-only dark:text-white">Search</label>
    <div class="relative">
      <div class="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none">
          <svg class="w-4 h-4 text-gray-500" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 20 20">
              <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="m19 19-4-4m0-7A7 7 0 1 1 1 8a7 7 0 0 1 14 0Z"/>
          </svg>
      </div>
      <input 
        type="search" 
        id="default-search" 
        class="block w-full p-4 pl-10 text-sm text-gray-900 border border-gray-300 rounded-lg bg-gray-50 focus:ring-green-500 focus-visible:border-vue-green-light focus:border-vue-green-light" 
        placeholder="Search quotes, speakers" 
        v-model="input"
        @input="onSearchInput"
        required
      >
      <button 
        type="submit" 
        class="text-gray-500 absolute right-2.5 bottom-2.5 bg-vue-green-light hover:bg-vue-green focus:ring-4 focus:outline-none focus:ring-green-300 font-medium rounded-lg text-sm px-4 py-2"
      >
        Search
      </button>
    </div>
  </form>
</template>
