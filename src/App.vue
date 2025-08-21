<template>
  <div class="flex items-center gap-20">
    <!-- Div du formulaire -->
    <div class="p-2 rounded-xl bg-gray-200 form-container" style="width:500px;"> 
      <form @submit.prevent="handleFormSubmit">
        <label class="input flex items-center gap-2">
          <!-- Icône de recherche -->
          <svg class="h-[1em] opacity-50" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
            <g stroke-linejoin="round" stroke-linecap="round" stroke-width="2.5" fill="none" stroke="currentColor">
              <circle cx="11" cy="11" r="8"></circle>
              <path d="m21 21-4.3-4.3"></path>
            </g>
          </svg>

          <!-- Champ de recherche -->
          <input ref="searchInput" type="search" class="grow" placeholder="Type company name" v-model="query" />

          <!-- Indication du raccourci -->
          <kbd class="kbd kbd-sm">{{ modifierKey }}</kbd>
          <kbd class="kbd kbd-sm">K</kbd>
        </label>
      </form>

      <!-- Résultats de l'API Google Custom Search -->
      <div class="mt-4" v-if="loading">
        <span class="loading loading-spinner loading-sm"></span>
      </div>
      <div class="mt-4 text-sm text-red-600" v-if="errorMessage">{{ errorMessage }}</div>
      <div class="mt-4 space-y-3" v-if="!loading && results.length">
        <div v-for="item in results" :key="item.link" class="p-3 rounded-lg bg-white shadow">
          <a :href="item.link" target="_blank" class="font-medium text-blue-600 hover:underline">{{ item.title }}</a>
          <p class="text-xs text-gray-600 mt-1" v-html="item.snippet"></p>
          <p class="text-[11px] text-gray-400 truncate">{{ item.link }}</p>
        </div>
      </div>
    </div>

    <!-- Div join-vertical -->
    <div class="p-2 rounded-xl">
      <div class="join join-vertical">
        <div class="join-item" style="padding: 20px;margin: 20px;background-color:#E5E7EB;border-radius: 5px;">
          <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="black"
            stroke-width="2" stroke-linecap="round" stroke-linejoin="round"
            class="lucide lucide-chrome-icon lucide-chrome">
            <circle cx="12" cy="12" r="10" />
            <circle cx="12" cy="12" r="4" />
            <line x1="21.17" x2="12" y1="8" y2="8" />
            <line x1="3.95" x2="8.54" y1="6.06" y2="14" />
            <line x1="10.88" x2="15.46" y1="21.94" y2="14" />
          </svg>
        </div>
        <div class="join-item" style="padding: 20px;margin: 20px;background-color: #E5E7EB;border-radius: 5px;">
          <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke=""
            stroke-width="2" stroke-linecap="round" stroke-linejoin="round"
            class="lucide lucide-chrome-icon lucide-chrome">
            <circle cx="12" cy="12" r="10" />
            <circle cx="12" cy="12" r="4" />
            <line x1="21.17" x2="12" y1="8" y2="8" />
            <line x1="3.95" x2="8.54" y1="6.06" y2="14" />
            <line x1="10.88" x2="15.46" y1="21.94" y2="14" />
          </svg>
        </div>
        <div class="join-item" style="padding: 20px;margin: 20px;background-color: #E5E7EB;border-radius: 5px;">
          <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="gray"
            stroke-width="2" stroke-linecap="round" stroke-linejoin="round"
            class="lucide lucide-chrome-icon lucide-chrome">
            <circle cx="12" cy="12" r="10" />
            <circle cx="12" cy="12" r="4" />
            <line x1="21.17" x2="12" y1="8" y2="8" />
            <line x1="3.95" x2="8.54" y1="6.06" y2="14" />
            <line x1="10.88" x2="15.46" y1="21.94" y2="14" />
          </svg>
        </div>
      </div>
    </div>

    <!-- Div join-vertical -->
    <div class="p-2 rounded-xl">
      <div style="padding: 20px;margin: 20px;background-color:#E5E7EB;border-radius: 5px;">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none"
          stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"
          class="lucide lucide-atom-icon lucide-atom">
          <circle cx="12" cy="12" r="1" />
          <path
            d="M20.2 20.2c2.04-2.03.02-7.36-4.5-11.9-4.54-4.52-9.87-6.54-11.9-4.5-2.04 2.03-.02 7.36 4.5 11.9 4.54 4.52 9.87 6.54 11.9 4.5Z" />
          <path
            d="M15.7 15.7c4.52-4.54 6.54-9.87 4.5-11.9-2.03-2.04-7.36-.02-11.9 4.5-4.52 4.54-6.54 9.87-4.5 11.9 2.03 2.04 7.36.02 11.9-4.5Z" />
        </svg>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";

const query = ref("");
const searchInput = ref(null);
const results = ref([]);
const loading = ref(false);
const errorMessage = ref("");

// Détection de l'OS pour afficher "⌘" ou "Ctrl"
const isMac = /Mac|iPod|iPhone|iPad/.test(navigator.platform);
const modifierKey = isMac ? "⌘" : "Ctrl";

// Shortcut simplifié avec conditions fusionnées
function handleShortcut(e) {
  if ((isMac && e.metaKey && e.key.toLowerCase() === "k") ||
      (!isMac && e.ctrlKey && e.key.toLowerCase() === "k")) {
    e.preventDefault();
    searchInput.value?.focus();
  }
}

// Appel à l'API JSON Custom Search
async function searchWithGoogleApi(searchText) {
  try {
    loading.value = true;
    errorMessage.value = "";
    results.value = [];

    const API_KEY = import.meta.env.VITE_API ?? import.meta.env.API;
    const SEARCH_ENGINE_ID = import.meta.env.VITE_SENGINE ?? import.meta.env.SENGINE;

    if (!API_KEY || !SEARCH_ENGINE_ID) {
      errorMessage.value = "Configuration manquante: VITE_API et VITE_SENGINE dans .env";
      console.error("Missing env: VITE_API or VITE_SENGINE");
      return;
    }

    const q = `${searchText} official instagram`;
    const params = new URLSearchParams({
      key: API_KEY,
      cx: SEARCH_ENGINE_ID,
      q,
      num: "10",
    });

    const response = await fetch(`https://www.googleapis.com/customsearch/v1?${params.toString()}`);
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }
    const data = await response.json();
    results.value = data.items ?? [];
  } catch (err) {
    console.error(err);
    errorMessage.value = "Erreur lors de l'appel à l'API Google Custom Search.";
  } finally {
    loading.value = false;
  }
}

// Gestionnaire pour le formulaire
function handleFormSubmit() {
  const value = query.value.trim();
  if (value) {
    searchWithGoogleApi(value);
  }
}

onMounted(() => {
  window.addEventListener("keydown", handleShortcut);
});

onBeforeUnmount(() => {
  window.removeEventListener("keydown", handleShortcut);
});
</script>
