<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink } from 'vue-router';

const tutores = ref([]); // lista de tutores vazia;

// chamando a minha API geral:
const API_URL = 'http://localhost:3000';

// chamar a minha API para listar todos os tutores;
async function carregarDados() {
  const respostaTutores = await fetch(`${API_URL}/tutores`);
  tutores.value = await respostaTutores.json();

  console.table(tutores.value);
}

async function removerTutor(id) {
  const confirmarExclusao = window.confirm('Deseja realmente excluir este tutor?');

  if (confirmarExclusao) {
    await fetch(`${API_URL}/tutores/${id}`, {
      method: 'DELETE',
    });
    await carregarDados(); // recarregar a lista de tutores após a exclusão  
  }
}

onMounted(carregarDados);
</script>

<template>
  <div>
    <header class="mb-4">
      <h1 class="text-2xl font-bold">Listagem de Tutores</h1>
      <p class="text-body-secondary mb-0">
        consulte os tutores e seus pets cadastrados.
      </p>
    </header>

    <RouterLink
      class="btn btn-outline-primary"
      to="tutores/novo"
    >
      Cadastrar Tutor
    </RouterLink>

    <table class="table table-striped table-hover">
      <thead>
        <tr>
          <th>id</th>
          <th>Nome</th>
          <th>Telefone</th>
          <th>Ações</th>
          <th>Email</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="tutor in tutores"
          :key="tutor.id"
        >
          <td>{{ tutor.id }}</td>
          <td>{{ tutor.nome }}</td>
          <td>{{ tutor.telefone }}</td>
          <td><RouterLink :to="`tutores/${tutor.id}`">Visualizar</RouterLink></td>
          <button
            class="btn btn-danger"
            @click="removerTutor(tutor.id)"
          >
            Remover
          </button>
        </tr>
      </tbody>
    </table>
  </div>
</template>
