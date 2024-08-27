<template>
  <div style="display: flex">
    <label>
      <input type="text" :value="role" @input="onRoleChange($event)" />
    </label>
    <button type="submit" @click="searchJob">Buscar</button>
  </div>
</template>

<script setup>
import { ref, watch, toRefs } from "vue";
import { useRouter, useRoute } from "vue-router";

// Recebendo `role` como uma prop da página principal
const props = defineProps({
  initialRole: String,
});

const emit = defineEmits(["update:role"]);

const role = ref(props.initialRole || "");

const router = useRouter();
const route = useRoute();

const onRoleChange = (event) => {
  role.value = event.target.value;
  emit("update:role", role.value); // Emitindo o evento para a página principal
};

function searchJob() {
  console.log("search", role.value);
  // Navega para a nova rota com os query params atualizados
  router.push({
    path: `/busca/${role.value}`,
    query: { ...route.query},
  });
}

// Watcher para atualizar o 'role' quando o parâmetro da rota mudar
watch(
  () => route.params.params,
  (newParams) => {
    role.value = newParams ? newParams[0] : "";
    emit("update:role", role.value); // Atualizando o valor de 'role' na página principal
  },
  { immediate: true }
);
</script>
