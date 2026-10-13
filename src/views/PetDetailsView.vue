<script setup>
import { onMounted, ref } from 'vue';
import { useRoute } from 'vue-router';

const route = useRoute();
const API_URL = 'http://localhost:3000';
const pet = ref(null);
const tutor = ref(null);

async function carregarPet() {
  const idPet = route.params.id;

  const respostaPet = await fetch(`$API_URL}/pets/${idPet}`);
  console.log(`respostaPet`, respostaPet);

  pet.value = await respostaPet.json();
  console.log(`pet`, pet.value);

  const respostaTutor = await fetch (`${API_URL}/tutores/${pet.value.tutorId}`);
  tutor.value = await respostaTutor.json();
  console.log(`Tutor`, `${tutor.value}`);
}

onMounted(carregarPet);
</script>

<template>
<section v-if=" pet ==null">
    <p>Carregandooooo..............................</p>
</section>

<section v-else>
 <h1>Nome do Pet: {{ pet.nome }}</h1>
  <p>Tutor: {{ tutor?.nome }}</p>
  <h5>Espécie {{ per.especie }}</h5>
</section>
</template>
