<template>
  <div class="bg-[#F6F8FC]">
    <div class="bg-rose-600 p-16">
      <Container>
        <NuxtImg src="/pokelogo.svg" class="mx-auto w-56" />
      </Container>
    </div>
    <Container class="h-screen">
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

      <div class="flex justify-between mx-auto max-w-screen-sm my-4">
        <button v-for="(value, index) in pokemon_types.results" :key="index">
          <img
            class="h-6 w-6"
            :src="`https://raw.githubusercontent.com/msikma/pokesprite/master/misc/types/gen8/${value.name}.png`"
            alt=""
          />
        </button>
      </div>

      <div v-if="status === 'pending'">Pending ...</div>
      <div v-else class="grid grid-cols-5 gap-5 mx-auto w-full">
        <NuxtLink
          v-for="(value, key) in data.pokemonList"
          :key="key"
          :to="`/search/${value.name}`"
          prefetch
        >
          <div
            class="relative rounded-3xl shadow-lg w-full mx-auto bg-white px-8 pb-8 mt-10 hover:scale-110"
          >
            <img
              class="h-[100px] mx-auto -mt-10"
              :src="`https://projectpokemon.org/images/normal-sprite/${value.name}.gif`"
              alt=""
            />

            <div class="bg-white mx-auto">
              <p class="text-center mt-4 text-[#999999] font-bold">
                #{{ value.id }}
              </p>
            </div>

            <div class="bg-white mx-auto flex justify-center gap-1">
              <p class="text-center text-[#011030] font-bold font-mono">
                {{ value.name.toUpperCase() }}
              </p>
            </div>

            <div class="flex gap-1 p-2 justify-center w-full">
              <img
                v-for="(stats, index) in value.stats"
                :key="index"
                :src="stats.sprites['generation-iii'].emerald.name_icon"
                alt="Image"
              />
            </div>
            <button
              class="h-[16px] w-[16px] text-[#999999] absolute bottom-4 right-4"
              @click.prevent="playCry(value.cries.latest)"
            >
              <Icon
                name="material-symbols:sound-detection-loud-sound"
                size="16"
              />
            </button>
          </div>
        </NuxtLink>
      </div>

      <Pagination
        class="mx-auto w-full flex justify-center mt-10"
        :totalItems="data.totalPokemons"
        :itemsPerPage="10"
        :modelValue="page + 1"
        @update="updatePagination"
      />
      <p class="text-center text-[#999999] mt-4 text-xs font-mono">
        Total Pokemons: {{ data.totalPokemons }}
      </p>
    </Container>
  </div>
</template>

<script setup>
import Container from "@/components/Container.vue";
import PokemonInput from "@/components/PokemonInput.vue";
import Pagination from "@/components/Pagination.vue";
import { ref, watch, onBeforeMount, onMounted, computed } from "vue";
import { useRoute, useRouter } from "vue-router";
import pokemonTypes from "../mocks/pokemonTypes.json";

const route = useRoute();
const router = useRouter();

const pokemon = ref(route.params.params ? route.params.params[0] : "");
const pokemon_types = ref(pokemonTypes);
const page = ref(route.query.page ? parseInt(route.query.page) - 1 : 0);
const type = ref(route.query.type ? parseInt(route.query.type) : "");
const loading = ref(false);

const endpoint = `https://pokeapi.co/api/v2/pokemon`;

const { data, status } = await useAsyncData(
  async () => {
      const res = await $fetch("https://pokeapi.co/api/v2/pokemon", {
      params: {
        limit: 10,
        offset: `${page.value}0`,
      },
    });

    const totalPokemons = await res.count;
    console.log("🚀 ~ resTotalPokemon:", totalPokemons);
    const pokemonList = [];

    for (let i = 0; i < res.results.length; i++) {
      const raw = await $fetch(res.results[i].url);
      const poke = await $fetch(`https://pokeapi.co/api/v2/pokemon/${raw.id}`);

      const stats = [];
      for (let j = 0; j < poke.types.length; j++) {
        const fetchStats = await $fetch(poke.types[j].type.url);
        stats.push(fetchStats);
      }

      pokemonList.push({
        id: raw.id,
        name: poke.name,
        cries: poke.cries,
        stats: stats,
      });
    }

    return {
      totalPokemons,
      pokemonList,
    };
  },
  {
    watch: [page],
    lazy: true,
  }
);

const updatePokemon = (newPokemon) => {
  pokemon.value = newPokemon;
};

const playCry = (value) => {
  const audio = new Audio(value);
  audio.volume = 0.2;
  audio.play();
};

function searchPokemon() {
  router.push({
    path: `/search/${pokemon.value}`,
  });
}

function updatePagination(value) {
  console.log("🚀 ~ updatePagination ~ value:", value);
  page.value = value - 1;
  router.push({ path: route.path, query: { page: page.value + 1 } });
}

watch(
  () => route.query.page,
  async (newPage) => {
    console.log("🚀 ~ watch:", newPage);
    page.value = newPage ? newPage - 1 : 0;
  }
);
</script>

<style scoped></style>
