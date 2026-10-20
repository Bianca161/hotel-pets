

<script setup>
import { onMounted, ref } from 'vue';
import { useRoute, RouterLink } from 'vue-router';

const route = useRoute();
const API_URL = 'http://localhost:3000';

const tutor = ref(null);
const petsDoTutor = ref([]); 

async function carregarTutorEPets() {
  const idTutor = route.params.id;

  const respostaTutor = await fetch(`${API_URL}/tutores/${idTutor}`);
  tutor.value = await respostaTutor.json();

  const respostaPets = await fetch(`${API_URL}/pets?tutorId=${idTutor}`);
  petsDoTutor.value = await respostaPets.json();
}

onMounted(carregarTutorEPets);
</script>

<template>
  <div class="container py-4">
    <header class="mb-4">
      <RouterLink to="/tutores" class="btn btn-outline-secondary mb-3">Voltar
      </RouterLink>
      <h1 class="text-2xl font-bold">Detalhes do Tutor</h1>
    </header>

    <div v-if="tutor === null" class="text-center py-5">
      <div class="spinner-border text-primary" role="status">
        <span class="visually-hidden">Carregando...</span>
      </div>
      <p class="mt-2">Carregando informações...</p>
    </div>

    <div v-else class="row gap-4">
      <div class="card p-3 shadow-sm col-md-5">
        <h4 class="card-title mb-3">Dados do Tutor</h4>
        <p><strong>ID:</strong> {{ tutor.id }}</p>
        <p><strong>Nome Completo:</strong> {{ tutor.nome }}</p>
        <p><strong>Telefone:</strong> {{ tutor.telefone }}</p>
           <p><strong>CPF/CNPJ:</strong> {{ tutor.cpf_cnpj }}</p>
        <p><strong>Email:</strong> {{ tutor.email }}</p>
      </div>

      <div class="card p-3 shadow-sm col-md-6">
        <h4 class="card-title mb-3">Pets Vinculados</h4>

        <div v-if="petsDoTutor.length === 0" class="alert alert-info mb-0">
          Este tutor ainda não possui pets cadastrados.
        </div>

        <ul v-else class="list-group list-group-flush">
          <li
            v-for="pet in petsDoTutor"
            :key="pet.id"
            class="list-group-item d-flex justify-content-between align-items-center"
          >
            <div>
              <strong>{{ pet.nome }}</strong> — <span class="text-muted">{{ pet.especie }}</span>
            </div>
            <RouterLink :to="`/pets/${pet.id}`" class="btn btn-sm btn-outline-primary">
              Ver Pet
            </RouterLink>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card {
  border-radius: 0.5rem;
}
</style>


