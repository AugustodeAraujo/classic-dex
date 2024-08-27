<template>
  <div>
    <div v-if="result">
      <img :src="result.sprites.front_default" alt="" />
    </div>
    <div v-else>não encontrado</div>
  </div>
</template>

<script setup>
const props = defineProps(["pokemon"]);
const endpoint = "https://pokeapi.co/api/v2/pokemon/";

const result = ref();

async function searchPokemon() {
  const data = await $fetch(`${endpoint}${props.pokemon}`);
  result.value = data;
  console.log(data);
}

onMounted(() => {
  searchPokemon();
});
</script>