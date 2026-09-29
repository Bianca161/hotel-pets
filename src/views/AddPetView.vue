<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink, useRouter } from 'vue-router';

const router = useRouter();
const API_URL = 'http://localhost:3000';

const tutores = ref([]);

const novoPet = ref({
  nome: '',
  especie: '',
  tutor: '',
});

async function carregarTutores() {
  const resposta = await fetch(`${API_URL}/tutores`);

  tutores.value = await resposta.json();
  console.table(tutores.value);
}

async function salvarPet() {
  await fetch(`${API_URL}/pets`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',

    },
    body: JSON.stringify(novoPet.value),
  })

  router.push('/pets');
}

onMounted(carregarTutores);
</script>

<template>
  <div>
    <header class="mb-4">
      <h1 class="text-2xl font-bold">Listagem de Pets</h1>
      <p class="text-body-secondary mb-0">Cadastro de Pets no sistema.</p>
    </header>

    <RouterLink
      class="btn btn-primary"
      :to="{ name: 'addPet' }"
    >
      Adicionar Pet
    </RouterLink>

    //me perdi aqui

    <form class="row" @submit.prevent="salvarPet">

      <div class="col-md-6">
       <label for="nome"
       class="form-label">

       </label>

      </div>

      <select
      name="especie"
      id="especie"
      class="form-select"
      required
      v-model="novoPet"
         v-model="novoPet.especie" 
      >

<option value="Cachorro"> Cachorro <option>
  <option value="Gato"> Gato <option>
    <option value="Peixe"> Peixe <option>
      <option value="Coelho"> Coelho <option>
        <option value="Cachorro"> Cachorro <option>
    </form>


            <option v-for="tutor in tutores" :key="tutor.id">{{tutor.nome}} </option>      
            
            <button class ="btn btn-sucess"></button>

</div>
</template>
