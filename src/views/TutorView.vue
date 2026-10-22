<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink } from 'vue-router';

const tutores = ref([]); // lista de tutores vazia;
const pets = ref([]);

// chamando a minha API geral:
const API_URL = 'http://localhost:3000';

// chamar a minha API para listar todos os tutores;
async function carregarDados() {
  const [resTutores, resPets] = await Promise.all([
    fetch(`${API_URL}/tutores`),
    fetch(`${API_URL}/pets`),
  ]);

  tutores.value = await resTutores.json();
  pets.value = await resPets.json();
}

function obterPetsDoTutor(tutorId) {
  return pets.value.filter((pet) => String(pet.tutorId) === String(tutorId));
}

function contarPets(tutorId) {
  return obterPetsDoTutor(tutorId).length;
}

function nomesDosPets(tutorId) {
  const listaPets = obterPetsDoTutor(tutorId);
  if (listaPets.length === 0) return 'Nenhum pet vinculado';
  return listaPets.map((p) => p.nome).join(' / ');
}

async function removerTutor(id) {
  // ADICIONADO: Verifica se o tutor possui pets vinculados antes de permitir a exclusão
  const qtdPets = contarPets(id);
  if (qtdPets > 0) {
    alert(`Não é possível excluir este tutor pois ele possui ${qtdPets} pet(s) vinculado(s). Remova ou transfira os pets primeiro.`);
    return;
  }

  const confirmarExclusao = window.confirm(
    'Deseja realmente excluir este tutor?',
  );

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
  <div class="container py-4">
    <header class="d-flex justify-content-between align-items-center mb-4">
      <div>
        <h1 class="text-2xl font-bold">Listagem de Tutores</h1>
        <p class="text-body-secondary mb-0">
          Consulte os tutores e seus pets cadastrados.
        </p>
      </div>
      <RouterLink
        class="btn btn-primary"
        to="/tutores/novo"
      >
        Cadastrar Tutor
      </RouterLink>
    </header>

    <div class="table-responsive">
      <table class="table table-striped table-hover align-middle">
        <thead>
          <tr>
            <th>ID</th>
            <th>Nome</th>
            <th>CPF/CNPJ</th>
            <th>Telefone</th>
            <th>E-mail</th>
            <th>Pets (Qtd)</th>
            <th>Nomes dos Pets</th>
            <th class="text-center">Ações</th>
          </tr>
        </thead>

        <tbody>
          <tr
            v-for="tutor in tutores"
            :key="tutor.id"
          >
            <td>{{ tutor.id }}</td>
            <td>{{ tutor.nome }}</td>
            <td>{{ tutor.cpf_cnpj }}</td>
            <td>{{ tutor.telefone }}</td>
            <td>{{ tutor.email }}</td>
            <td>
              <span class="badge bg-info text-dark">
                {{ contarPets(tutor.id) }}
              </span>
            </td>

            <td>{{ nomesDosPets(tutor.id) }}</td>

            <td class="text-center">
              <div class="d-flex gap-2 justify-content-center">
                <RouterLink
                  :to="`/tutores/${tutor.id}`"
                  class="btn btn-sm btn-outline-primary"
                >
                  Visualizar
                </RouterLink>
                <button
                  class="btn btn-sm btn-outline-danger"
                  @click="removerTutor(tutor.id)"
                >
                  Remover
                </button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<style scoped>
.table {
  vertical-align: middle;
}
</style>
