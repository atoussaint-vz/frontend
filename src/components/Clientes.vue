<template>
  <div>
    <h2>Lista de Clientes</h2>

    <!-- Formulario para agregar cliente -->
    <form @submit.prevent="agregarCliente" class="form">
      <input v-model="nuevo.nombre" placeholder="Nombre" required />
      <input v-model="nuevo.apellido" placeholder="Apellido" required />
      <input v-model="nuevo.email" placeholder="Correo" />
      <button type="submit">Agregar</button>
    </form>

    <!-- Mensajes de error -->
    <div v-if="error" class="error">{{ error }}</div>

    <!-- Lista de clientes -->
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

// API_URL desde variable de entorno (sin barra final)
const API_URL = (import.meta.env.VITE_API_URL || 'http://localhost:3000').replace(/\/$/, '')

// Cargar clientes desde el backend
async function cargarClientes() {
  try {
    const res = await fetch(`${API_URL}/clientes`)
    if (!res.ok) throw new Error('Error al obtener clientes')
    clientes.value = await res.json()
  } catch (err) {
    console.error(err)
    error.value = 'No se pudieron cargar los clientes. Revisa el backend.'
  }
}

// Agregar nuevo cliente
async function agregarCliente() {
  try {
    const res = await fetch(`${API_URL}/clientes`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(nuevo.value),
    })
    const data = await res.json()
    if (!res.ok) throw new Error(data.error || 'Error al agregar cliente')

    // Limpiar formulario y recargar clientes
    nuevo.value = { nombre: '', apellido: '', email: '' }
    await cargarClientes()
  } catch (err) {
    console.error(err)
    error.value = err.message
  }
}

// Cargar clientes al montar el componente
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
