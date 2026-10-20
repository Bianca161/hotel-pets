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
          <th>CPF/CNPJ</th>
          <th>Telefone</th>
          <th>E-mail</th>
          <th>Pets (Quantidade)</th>
          <th>Nomes dos Pets</th>
          <th>Ações</th>
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
         <td>{{ tutor.pets?.length || 0 }}</td>
         <td>{{ tutor.pets?.map(p => p.nome).join(' / ') || 'Nenhum pet vinculado' }}</td>

           <td>
            <RouterLink :to="`/tutores/${tutor.id}`" class="btn btn-sm">Visualizar</RouterLink>
            <button class="btn btn-danger btn-sm" @click="removerTutor(tutor.id)">
              Remover
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
div {
  padding: 20px;
  background-color: #fff;
}

/* Alinha o cabeçalho à esquerda e joga o botão "Cadastrar Tutor" para o canto superior direito */
div > header {
  display: inline-block;
  width: 70%;
}

div > .btn-outline-primary {
  float: right;
  margin-top: 10px;
}

/* Limpa o float para a tabela não subir */
.table {
  clear: both;
  margin-top: 20px;
  vertical-align: middle;
}

/* Estilização e espaçamento dos botões de ação na tabela */
tr td .btn-danger,
tr td a {
  margin-right: 5px;
}

/* Estilo visual para o link de Visualizar parecer um botão elegante */
tr td a {
  display: inline-block;
  padding: 0.375rem 0.75rem;
  font-size: 1rem;
  color: #0d6efd;
  border: 1px solid #0d6efd;
  border-radius: 0.375rem;
  text-decoration: none;
  transition: all 0.2s ease-in-out;
}

tr td a:hover {
  background-color: #0d6efd;
  color: #fff;
}

/* Ajuste fino para o botão de Remover ficar no padrão Bootstrap */
.btn-danger {
  padding: 0.375rem 0.75rem;
  font-size: 1rem;
  border-radius: 0.375rem;
}
</style>
