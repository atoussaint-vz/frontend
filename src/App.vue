<template>
  <div class="container">
    <header>
      <h1>Clientes — Frontend</h1>
    </header>

    <main>
      <Clientes />
    </main>
  </div>
</template>

<script setup>
import Clientes from './components/Clientes.vue'
</script>

<style>
.container {
  max-width: 900px;
  margin: 32px auto;
  font-family: system-ui, Segoe UI, Roboto, Helvetica, Arial;
}
header h1 {
  margin: 0 0 16px 0;
}
</style>
