<template>
  <div style="display: flex; flex-direction: column">
    <label
      for="poke"
      class="relative block rounded-l-full border border-gray-200 shadow-sm focus-within:border-cyan-600 focus-within:ring-1 focus-within:ring-cyan-600 p-4"
    >
      <input
        type="text"
        id="poke"
        class="peer border-none bg-transparent placeholder-transparent focus:border-transparent focus:outline-none focus:ring-0"
        placeholder="Procurar pokemon..."
        :value="role"
        @input="onRoleChange($event)"
        @focus="showSuggestions = true"
        @blur="hideSuggestions"
      />

      <span
        class="pointer-events-none absolute start-5 top-0 -translate-y-1/2 bg-cyan-600 p-1 text-sm text-white rounded-full px-4 transition-all peer-placeholder-shown:top-1/2 peer-placeholder-shown:text-sm peer-focus:top-0 peer-focus:text-sm peer-focus:text-white peer-focus:bg-cyan-600 peer-focus:rounded-full peer-focus:px-4"
      >
        Search pokemon name...
      </span>
    </label>

    <ul
      class="p-4 max-h-52 rounded-xl ml-5 shadow-md mx-auto z-10 absolute bg-[white] mt-14 overflow-y-auto w-[200px]"
      v-if="filteredSuggestions.length > 0"
    >
      <li
        class="hover:bg-rose-600 rounded-full hover:text-white hover:p-4"
        v-for="suggestion in filteredSuggestions"
        :key="suggestion.name"
        @mousedown="selectSuggestion(suggestion.name)"
        style="padding: 5px; cursor: pointer"
      >
        {{ suggestion.name }}
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from "vue";
import { useRouter, useRoute } from "vue-router";

const props = defineProps({
  initialRole: String,
});

const route = useRoute();
const emit = defineEmits(["update:role"]);

const role = ref(props.initialRole || "");
const result = ref([]);
const showSuggestions = ref(false);
const filteredSuggestions = ref([]);

// Methods
const onRoleChange = (event) => {
  role.value = event.target.value;
  emit("update:role", role.value);
  filterSuggestions();
};

const filterSuggestions = () => {
  const query = role.value.toLowerCase();
  filteredSuggestions.value = result.value.results.filter((poke) =>
    poke.name.toLowerCase().includes(query)
  );
};

const selectSuggestion = (name) => {
  role.value = name;
  emit("update:role", role.value);
  showSuggestions.value = false;
};

const hideSuggestions = () => {
  setTimeout(() => {
    showSuggestions.value = false;
  }, 100); // Timeout to allow click event on suggestion
};

async function suggestPokemon() {
  const endpoint = "https://pokeapi.co/api/v2/pokemon?limit=1000";
  const data = await $fetch(endpoint);
  result.value = data;
}

onMounted(() => {
  suggestPokemon();
});

watch(
  () => route.params.params,
  (newParams) => {
    role.value = newParams ? newParams[0] : "";
    emit("update:role", role.value);
  },
  { immediate: true }
);
</script>
