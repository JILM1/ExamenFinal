<template>
    <div class="p-6 max-w-4xl mx-auto">
      <h1 class="text-2xl font-bold mb-4">Gestión de Pólizas</h1>
  
      <form @submit.prevent="crearPoliza" class="mb-6 space-y-4">
        <input v-model="form.numeroPoliza" placeholder="Número de Póliza" class="input" />
        <input v-model="form.titular" placeholder="Titular" class="input" />
        <select v-model="form.tipoSeguro" class="input">
          <option disabled value="">Tipo de seguro</option>
          <option>Auto</option>
          <option>Vida</option>
          <option>Hogar</option>
          <option>Salud</option>
        </select>
        <input v-model.number="form.monto" placeholder="Monto" type="number" class="input" />
        <button type="submit" class="bg-blue-600 text-white px-4 py-2 rounded">Crear</button>
      </form>
  
      <div v-for="poliza in polizas" :key="poliza._id" class="p-4 border mb-2 rounded shadow">
        <p><strong>Número:</strong> {{ poliza.numeroPoliza }}</p>
        <p><strong>Titular:</strong> {{ poliza.titular }}</p>
        <p><strong>Tipo:</strong> {{ poliza.tipoSeguro }}</p>
        <p><strong>Monto:</strong> ${{ poliza.monto }}</p>
        <button @click="editarPoliza(poliza)" class="mt-2 bg-yellow-500 text-white px-3 py-1 rounded">Editar</button>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue'
  import axios from 'axios'
  
  const polizas = ref([])
  const form = ref({
    numeroPoliza: '',
    tipoSeguro: '',
    titular: '',
    monto: 0
  })
  const editandoId = ref(null)
  
  const fetchPolizas = async () => {
    const res = await axios.get('http://localhost:3000/api/polizas')
    polizas.value = res.data.data
  }
  
  const crearPoliza = async () => {
    try {
      if (editandoId.value) {
        await axios.put(`http://localhost:3000/api/polizas/${editandoId.value}`, form.value)
      } else {
        await axios.post('http://localhost:3000/api/polizas', form.value)
      }
      form.value = { numeroPoliza: '', tipoSeguro: '', titular: '', monto: 0 }
      editandoId.value = null
      await fetchPolizas()
    } catch (err) {
      alert('Error: ' + err.response.data.mensaje)
    }
  }
  
  const editarPoliza = (poliza) => {
    editandoId.value = poliza._id
    form.value = { ...poliza }
  }
  
  onMounted(fetchPolizas)
  </script>
  
  <style scoped>
  .input {
    display: block;
    width: 100%;
    padding: 0.5rem;
    border: 1px solid #ccc;
    border-radius: 0.375rem;
  }
  </style>
  