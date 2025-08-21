<template>
  <div class="flex items-center gap-20">
    <!-- Div du formulaire -->
    <div class="p-2 rounded-xl bg-gray-200 form-container shadow-md" style="width:300px;">
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

      <!-- <div class="mt-4 text-sm text-red-600" v-if="errorMessage">{{ errorMessage }}</div>
      <div class="mt-4 space-y-3" v-if="!loading && results.length">
        <div class="p-3 rounded-lg bg-white shadow">
          <a :href="results[0].link" target="_blank" class="font-medium text-blue-600 hover:underline">{{
            results[0].title }}</a>
          <p class="text-xs text-gray-600 mt-1" v-html="results[0].snippet"></p>
          <p class="text-[11px] text-gray-400 truncate">{{ results[0].link }}</p>
        </div>
      </div> -->
    </div>

    <!-- Div join-vertical -->
    <div class="p-2 rounded-xl shadow-md" style="text-align: center;color: gray;width:150px;">
      Search engines
      <div class="join join-vertical">
        <div class="join-item" style="padding: 20px;margin: 20px;background-color:#E5E7EB;border-radius: 5px;">
          <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none"
            stroke="#0fe043" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"
            class="lucide lucide-chrome-icon lucide-chrome">
            <circle cx="12" cy="12" r="10" />
            <circle cx="12" cy="12" r="4" />
            <line x1="21.17" x2="12" y1="8" y2="8" />
            <line x1="3.95" x2="8.54" y1="6.06" y2="14" />
            <line x1="10.88" x2="15.46" y1="21.94" y2="14" />
          </svg>
        </div>

        <div class="join-item" style="padding: 20px;margin: 20px;background-color: #E5E7EB;border-radius: 5px;">
          <img src="@/assets/openai-svgrepo-com.svg" alt="OpenAI" width="24" height="24"
            style="filter: brightness(0) saturate(100%) invert(50%) sepia(0%) saturate(0%) hue-rotate(0deg) brightness(100%) contrast(100%);" />
        </div>

        <div class="join-item" style="padding: 20px;margin: 20px;background-color: #E5E7EB;border-radius: 5px;">
          <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 48 48">
            <path fill="#808080"
              d="M17.572,37.076L20,35.619V10.603c0-1.632-0.796-3.161-2.133-4.096L12.36,2.652	C11.366,1.956,10,2.667,10,3.881V32.5c0,0.22,0.02,0.555,0.033,0.772C10.369,36.867,14.382,38.99,17.572,37.076z">
            </path>
            <path fill="#808080"
              d="M32.682,27.904L20,35.5v0l-2.428,1.457c-3.191,1.915-7.203-0.209-7.54-3.804	C10.372,38.922,15.145,43.5,21,43.5c1.963,0,3.888-0.536,5.568-1.551l6.834-4.126c0.817-0.493,1.522-1.075,2.15-1.707	C37.906,33.415,36.739,28.669,32.682,27.904z">
            </path>
            <path fill="#808080"
              d="M33.636,19.568l-7.607-3.803c-1.234-0.617-2.576,0.618-2.064,1.899l1.755,5.886	c0.499,1.248,1.479,2.242,2.719,2.758L32.5,28c4.057,0.766,5.352,5.251,3.052,8.117C40.399,31.24,40.088,22.794,33.636,19.568z">
            </path>
          </svg>
        </div>
      </div>
    </div>

    <!-- Div join-vertical -->
    <div class="p-2 rounded-xl" id="loader">
      <div v-if="loading" class="agglo"
        style="padding: 20px;margin: 20px;background-color:#E5E7EB;border-radius: 5px;text-align: center;">
        <span class="loading loading-spinner loading-xl"></span>
      </div>
      <div v-else class="agglo" style="padding: 20px;margin: 20px;background-color:#E5E7EB;border-radius: 5px;">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none"
          stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"
          class="lucide lucide-orbit-icon lucide-orbit">
          <path d="M20.341 6.484A10 10 0 0 1 10.266 21.85" />
          <path d="M3.659 17.516A10 10 0 0 1 13.74 2.152" />
          <circle cx="12" cy="12" r="3" />
          <circle cx="19" cy="5" r="2" />
          <circle cx="5" cy="19" r="2" />
        </svg>
      </div>
    </div>
    <!-- <div class="mt-4" v-if="loading">
        <span class="loading loading-spinner loading-sm"></span>
      </div> -->

    <div class="p-2 rounded-xl shadow-md" style="width:300px;">
      <div v-if="!loading && results.length" class="space-y-3">
        <h3 class="font-semibold text-gray-800 mb-2">Instagram : </h3>
        <div class="p-3 rounded-lg bg-white shadow">
          <a :href="results[0].link" target="_blank" class="font-medium text-blue-600 hover:underline">{{
            results[0].title }}</a>
          <p class="text-xs text-gray-600 mt-1" v-html="results[0].snippet"></p>
          <p class="text-[11px] text-gray-400 truncate">{{ results[0].link }}</p>
        </div>
      </div>
      <div v-else-if="loading" class="text-center">
        <span class="loading loading-spinner loading-sm"></span>
        <p class="text-sm text-gray-600 mt-2">Searching...</p>
      </div>
      
      <div v-if="!aboutLoading && aboutResults" class="space-y-3">
        <h3 class="font-semibold text-gray-800 mb-2">About : </h3>
        <div class="p-3 rounded-lg bg-white shadow">
          <p class="text-sm text-gray-700 leading-relaxed">{{ aboutResults }}</p>
        </div>
      </div>
      <div v-else-if="aboutLoading" class="text-center">
        <span class="loading loading-spinner loading-sm"></span>
        <p class="text-sm text-gray-600 mt-2">Recherche Knowledge Graph...</p>
      </div>

      <div v-else class="text-center text-gray-500">
        <p>Search resume</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";

const query = ref("");
const searchInput = ref(null);
const results = ref([]);
const aboutResults = ref("");
const loading = ref(false);
const aboutLoading = ref(false);
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

// Appel à l'API JSON Custom Search pour Instagram
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
      num: "1",
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

// Appel à l'API Knowledge Graph Search
async function searchKnowledgeGraph(searchText) {
  try {
    aboutLoading.value = true;
    aboutResults.value = "";

    const API_KEY = import.meta.env.VITE_GRAPHG;

    if (!API_KEY) {
      console.error("Missing env: VITE_GRAPHG");
      aboutResults.value = "Configuration manquante: VITE_GRAPHG dans .env";
      return;
    }

    // Utilisation de l'approche JSONP comme dans la documentation
    const service_url = 'https://kgsearch.googleapis.com/v1/entities:search';
    const params = {
      'query': searchText,
      'limit': 1,
      'indent': true,
      'key': API_KEY,
    };

    // Construction de l'URL avec les paramètres
    const queryString = Object.keys(params)
      .map(key => `${key}=${encodeURIComponent(params[key])}`)
      .join('&');
    
    const fullUrl = `${service_url}?${queryString}`;
    console.log("Knowledge Graph URL:", fullUrl);

    const response = await fetch(fullUrl);
    
    if (!response.ok) {
      if (response.status === 403) {
        throw new Error("Erreur 403: Vérifiez que votre clé API a accès à l'API Knowledge Graph Search");
      }
      throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    }
    
    const data = await response.json();
    console.log("Knowledge Graph response:", data);
    
    if (data.itemListElement && data.itemListElement.length > 0) {
      const entity = data.itemListElement[0].result;
      
      // Construire le texte à afficher avec description et detailedDescription
      let displayText = "";
      
      if (entity.description) {
        displayText += `Description: ${entity.description}\n\n`;
      }
      
      if (entity.detailedDescription && entity.detailedDescription.articleBody) {
        displayText += `Détails: ${entity.detailedDescription.articleBody}`;
      }
      
      aboutResults.value = displayText || "Aucune description disponible pour cette entité";
      
      console.log("Knowledge Graph result:", displayText.substring(0, 100));
      
    } else {
      aboutResults.value = "Aucune entité trouvée dans le Knowledge Graph";
    }
    
  } catch (err) {
    console.error("Erreur lors de la recherche Knowledge Graph:", err);
    
    if (err.message.includes("403")) {
      aboutResults.value = "Erreur 403: Vérifiez que votre clé API a accès à l'API Knowledge Graph Search. Assurez-vous que l'API est activée dans Google Cloud Console.";
    } else {
      aboutResults.value = `Erreur lors de la récupération des informations: ${err.message}`;
    }
  } finally {
    aboutLoading.value = false;
  }
}

// Gestionnaire pour le formulaire
function handleFormSubmit() {
  const value = query.value.trim();
  if (value) {
    // Lancer les deux recherches en parallèle
    searchWithGoogleApi(value);
    searchKnowledgeGraph(value);
  }
}

onMounted(() => {
  window.addEventListener("keydown", handleShortcut);
});

onBeforeUnmount(() => {
  window.removeEventListener("keydown", handleShortcut);
});
</script>
