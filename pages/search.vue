<template>
  <div>
    <div class="bg-rose-600 p-16">
      <Container>
        <NuxtImg src="/pokelogo.svg" class="mx-auto w-56" />
      </Container>
    </div>

    <Container>
      <div class="flex -mt-10 mb-10">
        <PokemonInput
          class="w-full bg-white rounded-l-full border"
          :initialRole="pokemon"
          @update:role="updatePokemon"
        />
        <button
          class="flex justify-center items-center p-4 border-t border-r border-b border-cyan-600 rounded-r-full bg-cyan-600 text-white shadow"
          type="submit"
          @click="searchPokemon"
        >
          <Icon name="material-symbols:search" size="24" color="white" />
        </button>
      </div>
    </Container>

    <!-- <PokemonInput :initialRole="pokemon" @update:role="updatePokemon" />
    <button type="submit" @click="searchPokemon">Buscar</button> -->
    <NuxtPage :pokemon="pokemon" />
  </div>
</template>

<script setup>
import { ref, watch } from "vue";
import { useRoute, useRouter } from "vue-router";
import PokemonInput from "@/components/PokemonInput.vue";


const route = useRoute();
const router = useRouter();

const pokemon = ref(route.params.params ? route.params.params[0] : "");


const updatePokemon = (newPokemon) => {
  pokemon.value = newPokemon;
};

function searchPokemon() {
  if (pokemon.value) {
    router.push({
      path: `/search/${pokemon.value}`,
    });
  } else {
    router.push({
      path: `/`,
    });
  }
}
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
