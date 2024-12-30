<template>
  <div>
    <h2 class="text-lg font-bold mb-4 text-green-700">Gestión de Deudas</h2>
    <form @submit.prevent="addDeuda">
      <select v-model="newDeuda.socioId" required>
        <option disabled value="">Selecciona un socio</option>
        <option v-for="socio in socios" :key="socio.id" :value="socio.id">
          {{ socio.name }}
        </option>
      </select>
      <input
        type="number"
        v-model="newDeuda.monto"
        placeholder="Monto de deuda"
        required
      />
      <button type="submit" class="text-lg font-bold mb-4 text-green-700">
        Agregar Deuda
      </button>
    </form>

    <!-- Botón para mostrar solo deudas mayores a 100 -->
    <button @click="toggleFiltroDeudaMayor" class="text-lg font-bold mb-4 text-green-700">
      {{ mostrarSoloDeudasAltas ? "Mostrar Todas las Deudas" : "Filtrar Deudas > 100" }}
    </button>

    <table>
      <thead>
        <tr>
          <th>Socio</th>
          <th>Monto</th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="deuda in filteredDeudas"
          :key="deuda.id"
          :class="{ 'bg-green-200': deuda.monto > 100 }"
        >
          <td>{{ getSocioName(deuda.socioId) }}</td>
          <td>{{ deuda.monto }}</td>
          <td>
            <button @click="deleteDeuda(deuda.id)" class="text-lg font-bold mb-4 text-green-700">
              Eliminar
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      deudas: [],
      socios: [],
      newDeuda: { socioId: "", monto: "" },
      mostrarSoloDeudasAltas: false, // Estado para filtrar deudas > 100
    };
  },
  computed: {
    filteredDeudas() {
      return this.mostrarSoloDeudasAltas
        ? this.deudas.filter((deuda) => deuda.monto > 100)
        : this.deudas;
    },
  },
  methods: {
    async fetchDeudas() {
      const response = await axios.get("http://localhost:3000/deudas");
      this.deudas = response.data;
    },
    async fetchSocios() {
      const response = await axios.get("http://localhost:3000/socios");
      this.socios = response.data;
    },
    getSocioName(socioId) {
      const socio = this.socios.find((s) => s.id === socioId);
      return socio ? socio.name : "Desconocido";
    },
    async addDeuda() {
      const response = await axios.post("http://localhost:3000/deudas", this.newDeuda);
      this.deudas.push(response.data);
      this.newDeuda = { socioId: "", monto: "" };
    },
    async deleteDeuda(id) {
      await axios.delete(`http://localhost:3000/deudas/${id}`);
      this.deudas = this.deudas.filter((deuda) => deuda.id !== id);
    },
    toggleFiltroDeudaMayor() {
      this.mostrarSoloDeudasAltas = !this.mostrarSoloDeudasAltas;
    },
  },
  mounted() {
    this.fetchDeudas();
    this.fetchSocios();
  },
};
</script>

<style>
/* Estilo verde para las filas con deudas mayores a 100 */
.bg-green-200 {
  background-color: #e6f4ea;
}
.text-green-700 {
  color: #059b50;
}
table th,
table td {
  border: 1px solid #375704;
  padding: 8px;
}
button {
  transition: background-color 0.2s;
}
input:focus {
  outline: none;
}
</style>
