<template scoped>
  <header class="mb-4">
    <h1 class="text2xl font-bold">Listagem de Pets</h1>
    <p class="text-body-secodary mb-0">
      Listagem dos Pets cadastrados no sistema
    </p>
  </header>

  <RouterLink
    class="btn btn-primary"
    :to="{ name: 'novo-pet' }"
  >
    <i
      class="bi bi-plus me-2"
      aria-hidden="true"
    ></i>
    Adicionar Pet
  </RouterLink>
</template>
