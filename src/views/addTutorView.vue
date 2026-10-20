<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink, useRouter } from 'vue-router';

const router = useRouter(); // chamando meu router
// chamando a minha API
const API_URL = 'http://localhost:3000';

// lista vazia dos meus tutores
const tutores = ref([]);

// lista de cep dos meus tutores
const cepApi = ref({});

// criar objeto para salvar um novo tutor
// propriedades: nome, telefone e email

const novoTutor = ref({
  nome: '',
  telefone: '',
  cpf_cnpj: '',
  email: '',
  data: '',

  pets: {
    nomePet: '',
    especiePet: '',
    quantidadePet: 0
},

  endereco: {
    rua: '',
    numero: '',
    bairro: '',
    cidade: '',
    estado: '',
    cep: '',
  },
});

// cpf direto
async function buscaCep(cep) {
  const API_CEP = 'https://viacep.com.br/ws';
  const respostaCep = await fetch(`${API_CEP}/${cep}/json/`);

  cepApi.value = await respostaCep.json();

  novoTutor.value.endereco.rua = cepApi.value.logradouro;
  novoTutor.value.endereco.bairro = cepApi.value.bairro;
  novoTutor.value.endereco.cidade = cepApi.value.localidade;
  novoTutor.value.endereco.estado = cepApi.value.uf;
}

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

    <!---- parte do formulario -->
     <div class="row">
        <div class="col-md-12 mt-3">
          <h1 class="text-2xl font-bold">Dados do Tutor</h1>
        </div>
      </div>

      <!-- nome do tutor -->
      <div class="col-md-6">
        <label
          for="nome"
          class="form-label"
        >
          Nome Completo
        </label>
        <input
          type="text"
          class="form-control"
          id="nome"
          v-model="novoTutor.nome"
          required
        />
      </div>

      <!-- CPF/CNPJ do tutor -->
      <div class="col-md-6">
        <label
          for="cpf_cnpj"
          class="form-label"
        >
          CPF/CNPJ
        </label>
        <input
          type="text"
          class="form-control"
          id="cpf_cnpj"
          v-model="novoTutor.cpf_cnpj"
          required
        />
      </div>

      <!-- telefone do tutor -->
      <div class="col-md-6">
        <label
          for="telefone"
          class="form-label"
        >
          Telefone/Celular/Whatsapp
        </label>
        <input
          type="text"
          class="form-control"
          id="telefone"
          v-model="novoTutor.telefone"
          required
        />
      </div>

      <!-- Email do tutor -->
      <div class="col-md-6">
        <label
          for="email"
          class="form-label"
        >
          Email
        </label>
        <input
          type="text"
          class="form-control"
          id="email"
          v-model="novoTutor.email"
          required
        />
      </div>

      <!-- Data Nascimento do tutor -->
      <div class="col-md-6">
        <label
          for="data"
          class="form-label"
        >
          Data Nascimento
        </label>
        <input
          type="date"
          class="form-control"
          id="data"
          v-model="novoTutor.data"
          required
        />
      </div>

      <div class="col-12 d-flex gap-2">

        <RouterLink
          class="btn btn-secondary mt-3"
          to="/tutores"
        >
          Cancelar
        </RouterLink>

        <button
          class="btn btn-success
          type mt-3"
        >
          Cadastrar Tutor
        </button>
      </div>

      <!---2 Parte ----->

      <div class="row">
        <div class="col-md-12 mt-3">
          <h4 class="text-2xl font-bold">Endereço</h4>
        </div>
      </div>

      <!-- CEP do Tutor -->
      <div class="row">
        <div class="col-md-4">
          <label
            for="cep"
            class="form-label"
          >
            CEP
          </label>
          <input
            type="text"
            class="form-control"
            id="cep"
            v-model="novoTutor.endereco.cep"
            required
            @blur="buscaCep(novoTutor.endereco.cep)"
          />
        </div>

        <div class="col-12 col-md-4">
          <!-- RUA -->
          <label
            for="rua"
            class="form-label"
          >
            Rua:
          </label>
          <input
            type="text"
            class="form-control"
            id="rua"
            v-model="novoTutor.endereco.rua"
            required
          />
        </div>
        <!-- Telefone do Tutor -->
        <div class="col-md-4">
          <label
            for="numero"
            class="form-label"
          >
            Número:
          </label>
          <input
            type="text"
            class="form-control"
            id="numero"
            v-model="novoTutor.endereco.numero"
            required
          />
        </div>
      </div>

      <div class="row">
        <div class="col-md-4">
          <label
            for="bairro"
            class="form-label"
          >
            Bairro
          </label>
          <input
            type="text"
            class="form-control"
            id="bairro"
            v-model="novoTutor.endereco.bairro"
            required
          />
        </div>

        <div class="col-12 col-md-4">
          <!-- Cidade -->
          <label
            for="cidade"
            class="form-label"
          >
            Cidade:
          </label>
          <input
            type="text"
            class="form-control"
            id="cidade"
            v-model="novoTutor.endereco.cidade"
            required
          />
        </div>
        <!-- Estado -->
        <div class="col-md-4">
          <label
            for="estado"
            class="form-label"
          >
            Estado:
          </label>
          <input
            type="text"
            class="form-control"
            id="estado"
            v-model="novoTutor.endereco.estado"
            required
          />
        </div>
        </div> 
    </form>
  </div>
</template>
