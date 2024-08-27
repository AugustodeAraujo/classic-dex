<template>
  <div>
    <h2>Buscar cargo</h2>
    <RoleInput :initialRole="role" @update:role="updateRole" />

    <h3>Filtrar por Cidade</h3>
    <div style="position: relative">
      <button @click="toggleDropdown">
        {{ dropdownOpen ? "Fechar" : "Escolha as cidades" }}
      </button>

      <div v-if="dropdownOpen" class="dropdown-menu">
        <div v-for="city in cities" :key="city">
          <label>
            <input
              type="checkbox"
              :value="city"
              :checked="isCitySelected(city)"
              @change="onCityChange($event)"
            />
            {{ city }}
          </label>
        </div>
      </div>
    </div>
    {{ counter }}
   

    <NuxtPage
      :counterProps="counter"
      @eventoCustomizado="handleEventoCustomizado"
    />
  </div>
</template>

<script setup>
import { ref, watch } from "vue";
import { useRoute, useRouter } from "vue-router";
import RoleInput from "@/components/RoleInput.vue";

const router = useRouter();
const route = useRoute();

const role = ref(route.params.params ? route.params.params[0] : "");
const cities = ref(["São Paulo", "Brasília", "Rio de Janeiro"]);
const selectedCities = ref(route.query.city ? route.query.city.split(",") : []);

// Controla a exibição do dropdown
const dropdownOpen = ref(false);

const toggleDropdown = () => {
  dropdownOpen.value = !dropdownOpen.value;
};

const updateRole = (newRole) => {
  role.value = newRole;
};

const onCityChange = (event) => {
  const city = event.target.value;

  if (event.target.checked) {
    // Adiciona a cidade à lista de selecionados
    selectedCities.value.push(city);
  } else {
    // Remove a cidade da lista de selecionados
    selectedCities.value = selectedCities.value.filter((c) => c !== city);
  }

  const wasDropdownOpen = dropdownOpen.value; // Salva o estado atual do dropdown

  // Atualiza a URL com as cidades selecionadas
  router
    .push({
      query: { ...route.query, city: selectedCities.value.join(",") },
    })
    .then(() => {
      dropdownOpen.value = wasDropdownOpen; // Restaura o estado do dropdown após a navegação
    });
};

// Verifica se uma cidade está selecionada
const isCitySelected = (city) => {
  return selectedCities.value.includes(city);
};

// Watcher para sincronizar a lista de cidades selecionadas com o query param 'city'
watch(
  () => route.query.city,(newCity) => {
    selectedCities.value = newCity ? newCity.split(",") : [];
  }
);
</script>

<style scoped>
.dropdown-menu {
  position: absolute;
  background-color: white;
  border: 1px solid #ccc;
  padding: 10px;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
  z-index: 1000;
}

.dropdown-menu div {
  margin-bottom: 5px;
}
</style>
