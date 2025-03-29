<template>
  <div class="p-6 max-w-5xl mx-auto">
    <div class="bg-white p-6 rounded-xl shadow mb-10">
      <h2 class="text-3xl font-semibold mb-6 text-blue-800">Gestión de Pólizas</h2>
      <form @submit.prevent="crearPoliza" class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <div>
          <label class="block mb-1 font-medium">Número de Póliza</label>
          <input v-model="form.numeroPoliza" placeholder="Ej: PZ-001" class="form-input" />
        </div>
        <div>
          <label class="block mb-1 font-medium">Titular</label>
          <input v-model="form.titular" placeholder="Ej: María Gómez" class="form-input" />
        </div>
        <div>
          <label class="block mb-1 font-medium">Tipo de Seguro</label>
          <select v-model="form.tipoSeguro" class="form-input">
            <option disabled value="">Seleccione</option>
            <option>Auto</option>
            <option>Vida</option>
            <option>Hogar</option>
            <option>Salud</option>
          </select>
        </div>
        <div>
          <label class="block mb-1 font-medium">Monto</label>
          <input v-model.number="form.monto" type="number" class="form-input" placeholder="Ej: 20000" />
        </div>
        <div class="col-span-2 text-right">
          <button
            type="submit"
            class="mt-4 inline-block bg-blue-600 text-white px-6 py-2 rounded-lg shadow hover:bg-blue-700 transition">
            {{ editandoId ? 'Actualizar' : 'Crear' }} Póliza
          </button>
        </div>
      </form>
    </div>

    <div class="grid gap-6 grid-cols-1 md:grid-cols-2">
      <div v-for="poliza in polizas" :key="poliza._id" class="bg-white p-5 rounded-lg shadow-lg border">
        <h3 class="text-xl font-bold text-gray-800 mb-2">{{ poliza.numeroPoliza }}</h3>
        <p class="text-gray-600"><strong>Titular:</strong> {{ poliza.titular }}</p>
        <p class="text-gray-600">
          <strong>Tipo:</strong>
          <span
            class="inline-block px-2 py-1 text-xs font-semibold rounded bg-blue-100 text-blue-800 uppercase">
            {{ poliza.tipoSeguro }}
          </span>
        </p>
        <p class="text-gray-600"><strong>Monto:</strong> ${{ poliza.monto }}</p>
        <button
          @click="editarPoliza(poliza)"
          class="mt-4 bg-yellow-500 text-white px-4 py-1 rounded hover:bg-yellow-600 transition">
          Editar
        </button>
      </div>
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
.form-input {
  @apply w-full px-4 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-400;
}
</style>
