<template>
  <div>
    <h2>Lista de Clientes</h2>

    <form @submit.prevent="agregarCliente" class="form">
      <input v-model="nuevo.nombre" placeholder="Nombre" required />
      <input v-model="nuevo.apellido" placeholder="Apellido" required />
      <input v-model="nuevo.email" placeholder="Correo" />
      <button type="submit">Agregar</button>
    </form>

    <div v-if="error" class="error">{{ error }}</div>

    <div v-else-if="clientes.length === 0">
      <p>No hay clientes. Inserta uno para probar.</p>
    </div>

    <ul v-else>
      <li v-for="cliente in clientes" :key="cliente.cod_cliente">
        {{ cliente.nombre }} {{ cliente.apellido }} — {{ cliente.email ?? '—' }}
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const clientes = ref([])
const error = ref('')
const nuevo = ref({ nombre: '', apellido: '', email: '' })

const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:3000'

async function cargarClientes() {
  try {
    const res = await fetch(`${API_URL}/clientes`)
    if (!res.ok) throw new Error('Error al obtener clientes')
    clientes.value = await res.json()
  } catch (err) {
    error.value = err.message
  }
}

async function agregarCliente() {
  try {
    const res = await fetch(`${API_URL}/clientes`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(nuevo.value),
    })
    if (!res.ok) {
      const data = await res.json()
      throw new Error(data.error || 'Error al agregar cliente')
    }
    nuevo.value = { nombre: '', apellido: '', email: '' }
    await cargarClientes()
  } catch (err) {
    error.value = err.message
  }
}

onMounted(cargarClientes)
</script>

<style scoped>
.form {
  display: flex;
  gap: 8px;
  margin-bottom: 16px;
}
.error {
  color: red;
  font-weight: bold;
}
</style>
