<script setup>
import { onMounted, ref } from 'vue';
import { useRoute, RouterLink } from 'vue-router';

const route = useRoute();
const API_URL = 'http://localhost:3000';

const tutor = ref(null);
const petsDoTutor = ref([]); 

async function removerPets(id) {
  const confirmarExclusao = window.confirm('Deseja realmente excluir este pet?');

  if (confirmarExclusao) {
    await fetch(`${API_URL}/pets/${id}`, {
      method: 'DELETE',
    });
    await carregarTutorEPets(); // recarregar a lista de tutores após a exclusão
  }
}

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
    <header class="mb-4 d-flex justify-content-between align-items-center">
      <div>
        <h1 class="text-2xl font-bold">Detalhes do Tutor</h1>
        <p class="text-body-secondary mb-0">Visualize os dados do tutor e seus pets vinculados.</p>
      </div>
      <RouterLink to="/tutores" class="btn btn-outline-secondary">
       Voltar para Tutores
      </RouterLink>
    </header>

    <div v-if="tutor === null" class="text-center py-5">
      <div class="spinner-border text-primary" role="status">
        <span class="visually-hidden">Carregando...</span>
      </div>
      <p class="mt-2">Carregando informações...</p>
    </div>

    <div v-else class="d-flex flex-column gap-4">

      <!-- Card com Dados do Tutor -->
      <div class="card p-4 shadow-sm">
        <h4 class="card-title mb-3">Dados do Tutor</h4>

        <div class="row g-3">
          <div class="col-md-4">
            <p class="mb-1 text-muted">ID</p>
            <p class="fw-semibold">{{ tutor.id }}</p>
          </div>

          <div class="col-md-4">
            <p class="mb-1 text-muted">Nome Completo</p>
            <p class="fw-semibold">{{ tutor.nome }}</p>
          </div>

          <div class="col-md-4">
            <p class="mb-1 text-muted">CPF / CNPJ</p>
            <p class="fw-semibold">{{ tutor.cpf_cnpj }}</p>
          </div>

           <div class="col-md-4">
            <p class="mb-1 text-muted">Telefone / Celular / WhatsApp</p>
            <p class="fw-semibold">{{ tutor.telefone }}</p>
          </div>
          
          <div class="col-md-4">
            <p class="mb-1 text-muted">E-mail</p>
            <p class="fw-semibold">{{ tutor.email }}</p>
          </div>

          <div class="col-md-4">
            <p class="mb-1 text-muted">Data de Nascimento</p>
            <p class="fw-semibold">{{ tutor.data }}</p>
          </div>
        </div>
      </div>

      <!-- Card com Pets Vinculados -->
      <div class="card p-4 shadow-sm">
        <div class="d-flex align-items-center gap-2 mb-3">

          <h4 class="card-title mb-0">Pets vinculados</h4>
          <span class="badge bg-primary rounded-pill">{{ petsDoTutor.length }} pets</span>
        </div>

        <div v-if="petsDoTutor.length === 0" class="alert alert-info mb-0">
          Este tutor ainda não possui pets cadastrados.
        </div>

        <table v-else class="table table-striped table-hover align-middle mb-0">
          <thead>
            <tr>
              <th>ID</th>
              <th>Nome</th>
              <th>Espécie</th>
              <th>Ações</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="pet in petsDoTutor" :key="pet.id">
              <td>{{ pet.id }}</td>
              <td>{{ pet.nome }}</td>
              <td>{{ pet.especie }}</td>
              <td>
                <button class="btn btn-sm btn-outline-danger" @click="removerPets(pet.id)">
                  Excluir
                </button>
                <RouterLink :to="`/pets/${pet.id}`" class="btn btn-sm btn-outline-primary">
                  Visualizar Pet
                </RouterLink>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card {
  border-radius: 0.5rem;
}
</style>


