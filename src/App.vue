<script setup>
import ListaAlumnos from './components/ListaAlumnos.vue'
</script>

<template>
  <header>
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />
    <h1 class="text-2xl font-bold">Gestión de Alumnos</h1>
  </header>

  <main>
    <ListaAlumnos />
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
  text-align: center;
  margin-bottom: 2rem;
}

.logo {
  display: block;
  margin: 0 auto 1rem;
}
</style>
