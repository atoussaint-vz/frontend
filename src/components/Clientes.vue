<template>
  <section>
    <h2>Lista de clientes</h2>

    <div v-if="loading">Cargando clientes…</div>

    <div v-else>
      <div v-if="error" class="error">{{ error }}</div>

      <div v-else-if="clientes.length === 0">
        <p>No hay clientes. Inserta uno para probar (o revisa la base).</p>
      </div>

      <table v-else class="table">
        <thead>
          <tr><th>ID</th><th>Nombre</th><th>Apellido</th><th>Email</th></tr>
        </thead>
        <tbody>
          <tr v-for="c in clientes" :key="c.cod_cliente">
            <td>{{ c.cod_cliente }}</td>
            <td>{{ c.nombre }}</td>
            <td>{{ c.apellido }}</td>
            <td>{{ c.email ?? '—' }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const clientes = ref([])
const loading = ref(true)
const error = ref(null)

onMounted(async () => {
  try {
    // Si usas proxy en vite.config.js usa '/clientes'
    // Si no usas proxy usa la URL completa: 'http://localhost:3000/clientes'
    const res = await fetch('/clientes')
    if (!res.ok) throw new Error(`HTTP ${res.status}`)
    clientes.value = await res.json()
  } catch (err) {
    console.error(err)
    error.value = 'No se pudieron cargar los clientes. Revisa el backend.'
  } finally {
    loading.value = false
  }
})
</script>

<style scoped>
.table { width:100%; border-collapse: collapse; margin-top:12px; }
.table th, .table td { border:1px solid #ddd; padding:8px; text-align:left; }
.error { color: #a33; margin-bottom:12px; }
</style>
