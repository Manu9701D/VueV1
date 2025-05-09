<script setup>
import ListaAlumnos from "./components/ListaAlumnos.vue";
</script>

<template>
  <header>
    <img
      alt="Vue logo"
      class="logo"
      src="./components/icons/logo.png"
      width="200"
      height="200"
    />
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
