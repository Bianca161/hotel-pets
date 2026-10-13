<script setup>
import { onMounted, ref } from 'vue';
import { useRoute } from 'vue-router';

const route = useRoute();
const API_URL = 'http://localhost:3000';
const tutor = ref(null);

async function carregarTutor() {
  // pegando o id do tutor que está na url do navegador
  const idTutor = route.params.id;

  // chamando a API para buscar o tutor pelo id
  const respostaTutor = await fetch(`${API_URL}/tutores/${idTutor}`);
  console.log('respostaTutor', respostaTutor    );
  // convertendo a resposta da API para JSON
  tutor.value = await respostaTutor.json();
  console.log('tutor', tutor.value);
}

onMounted(carregarTutor);
</script>

<template>
  <section v-if="tutor === null">
    <p>Carregandooo.....</p>
  </section>

  <section v-else>
    <h1>Detalhes do Tutor</h1>
    <p>Visualize os dados do tutor e seus pets vinculados.</p>
    <h4>Dados do Tutor</h4>

     <p>Id: {{ tutor.id }}</p>
    <p>Nome Completo: {{ tutor.nome }}</p>
    <p>Telefone: {{ tutor.telefone }}</p>
  </section>
</template>


