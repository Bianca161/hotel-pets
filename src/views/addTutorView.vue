<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink, useRouter } from 'vue-router';

const router = useRouter(); // chamando meu router
// chamando a minha API
const API_URL = 'http://localhost:3000';

// lista vazia dos meus tutores
const tutores = ref([]);

// criar objeto para salvar um novo tutor
// propriedades: nome, telefone e email

const novoTutor = ref({
  nome: '',
  telefone: '',
});

// buscar todos os tutores que estão salvo na aplicação
async function carregarTutores() {
  const resposta = await fetch(`${API_URL}/tutores`);

  // converter os dados da minha API que estão em JSON para JS
  tutores.value = await resposta.json();
  console.table(tutores.value);
}

// salvar o novo tutor no sistema
async function salvarTutor() {
  await fetch(`${API_URL}/tutores`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(novoTutor.value),
  });

  // redirecionar para a tela de listagem de Tutores
  router.push('/tutores');
}

onMounted(carregarTutores);
</script>

<template>
  <div>
    <header class="mb-4">
      <h1 class="text-2xl font-bold">Cadastrar Tutores</h1>
      <p class="text-body-secondary mb-0">
        Prencha os dados para Cadastrar um novo Tutor.
      </p>
    </header>

    <RouterLink
      class="btn btn-outline-primary"
      :to="{ name: 'tutores' }"
    >
      Lista Tutores
    </RouterLink>

    <form
      class="row"
      @submit.prevent="salvarTutor"
    >

    <h1>Dados do Tutor </h1>
      <!-- ID do tutor -->
      <div class="col-md-6">
        <label
          for="ID"
          class="form-label"
        >
          ID do Tutor:
        </label>
        <input
          type="text"
          class="form-control"
          id="ID"
          v-model="novoTutor.ID"
          required
        />
      </div>

      <!-- nome do tutor -->
      <div class="col-md-6">
        <label
          for="nome"
          class="form-label"
        >
          Nome do Tutor:
        </label>
        <input
          type="text"
          class="form-control"
          id="nome"
          v-model="novoTutor.nome"
          required
        />
      </div>

      <!-- telefone do tutor -->
      <div class="col-md-6">
        <label
          for="telefone"
          class="form-label"
        >
          Telefone do Tutor:
        </label>
        <input
          type="text"
          class="form-control"
          id="telefone"
          v-model="novoTutor.telefone"
          required
        />
      </div>

      <div class="col-12 d-flex gap-2">
        <button
          class="btn btn-success"
          type="submit"
        >
          Cancelar
        </button>

        <button
          class="btn btn-success"
          type="submit"
        >
          Cadastrar Tutor
        </button>
      </div>
    </form>
  </div>
</template>
