<script setup>
import { ref, computed } from 'vue';
import { RouterLink, useRouter } from 'vue-router';

const router = useRouter(); // chamando meu router
// chamando a minha API
const API_URL = 'http://localhost:3000';

// lista de cep dos meus tutores
const cepApi = ref({});

// Adicionado: controle para exibir o erro somente após tentar salvar
const formularioSubmetido = ref(false);

// criar objeto para salvar um novo tutor
// propriedades: nome, telefone e email

const novoTutor = ref({
  nome: '',
  telefone: '',
  cpf_cnpj: '',
  email: '',
  data: '',
  endereco: {
    rua: '',
    numero: '',
    bairro: '',
    cidade: '',
    estado: '',
    cep: '',
  },
});

// Função para aplicar máscara de telefone (XX) XXXXX-XXXX ou (XX) XXXX-XXXX
function aplicarMascaraTelefone(event) {
  let valor = event.target.value.replace(/\D/g, '');
  if (valor.length > 11) valor = valor.slice(0, 11);

  if (valor.length > 10) {
    valor = valor.replace(/^(\d{2})(\d{5})(\d{4})$/, '($1) $2-$3');
  } else if (valor.length > 5) {
    valor = valor.replace(/^(\d{2})(\d{4})(\d{0,4})$/, '($1) $2-$3');
  } else if (valor.length > 2) {
    valor = valor.replace(/^(\d{2})(\d{0,5})$/, '($1) $2');
  }

  novoTutor.value.telefone = valor;
}

// Função para aplicar máscara dinâmica de CPF (11 dígitos) ou CNPJ (14 dígitos)
function aplicarMascaraCpfCnpj(event) {
  let valor = event.target.value.replace(/\D/g, '');
  if (valor.length > 14) valor = valor.slice(0, 14);

  if (valor.length > 11) {
    // Máscara CNPJ: XX.XXX.XXX/XXXX-XX
    valor = valor.replace(
      /^(\d{2})(\d{3})(\d{3})(\d{4})(\d{2})$/,
      '$1.$2.$3/$4-$5',
    );
  } else if (valor.length > 9) {
    // Máscara CPF: XXX.XXX.XXX-XX
    valor = valor.replace(/^(\d{3})(\d{3})(\d{3})(\d{2})$/, '$1.$2.$3-$4');
  }

  novoTutor.value.cpf_cnpj = valor;
}

// Adicionado: propriedades computadas para validar Nome e Telefone
const nomeInvalido = computed(
  () => formularioSubmetido.value && !novoTutor.value.nome.trim(),
);
const telefoneInvalido = computed(
  () => formularioSubmetido.value && !novoTutor.value.telefone.trim(),
);

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

async function salvarTutor() {
  // Adicionado: ativa a exibição dos erros
  formularioSubmetido.value = true;

  // Adicionado: impede o envio se Nome ou Telefone estiverem vazios
  if (!novoTutor.value.nome.trim() || !novoTutor.value.telefone.trim()) {
    return;
  }

  await fetch(`${API_URL}/tutores`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(novoTutor.value),
  });

  router.push('/tutores');
}
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
      novalidate
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
          :class="{ 'is-invalid': nomeInvalido }"
          id="nome"
          v-model="novoTutor.nome"
        />
        <!-- Adicionado: mensagem exigida pelo professor -->
        <div
          v-if="nomeInvalido"
          class="invalid-feedback"
        >
          Ops, campo obrigatório, por favor preencha-o!
        </div>
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
          @input="aplicarMascaraCpfCnpj"
          placeholder="000.000.000-00 ou 00.000.000/0001-00"
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
          :class="{ 'is-invalid': telefoneInvalido }"
          id="telefone"
          v-model="novoTutor.telefone"
          @input="aplicarMascaraTelefone"
        />
        <!-- Adicionado: mensagem exigida pelo professor -->
        <div
          v-if="telefoneInvalido"
          class="invalid-feedback"
        >
          Ops, campo obrigatório, por favor preencha-o!
        </div>
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
          type="email"
          class="form-control"
          id="email"
          v-model="novoTutor.email"
          placeholder="exemplo@email.com"
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
          class="btn btn-success mt-3"
          type="submit"
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
