<template>
  <div>
    <h2 class="text-lg font-bold mb-4 text-green-700">Gestión de Reservas de Eventos</h2>
    <!-- Formulario para agregar o actualizar una reserva -->
    <form @submit.prevent="submitReserva">
      <input type="date" v-model="currentReserva.fechaEvento" required />
      <input type="time" v-model="currentReserva.horaEvento" required />
      <select v-model="currentReserva.tipoEvento" required>
        <option disabled value="">Selecciona tipo de evento</option>
        <option value="Torneo">Torneo</option>
        <option value="Clase">Clase</option>
        <option value="Partido_Amistoso">Partido Amistoso</option>
      </select>
      <select v-model="currentReserva.socioId" required>
        <option disabled value="">Selecciona un socio</option>
        <option v-for="socio in socios" :key="socio.id" :value="socio.id">
          {{ socio.name }}
        </option>
      </select>
      <button type="submit" class="text-lg font-bold mb-4 text-green-700">
        {{ currentReserva.id ? "Actualizar Reserva" : "Agregar Reserva" }}
      </button>
    </form>

    <!-- Búsqueda -->
    <input
      type="text"
      v-model="searchQuery"
      placeholder="Buscar por tipo de evento"
      class="border p-2"
    />

    <!-- Listado de reservas -->
    <table>
      <thead>
        <tr>
          <th>Fecha</th>
          <th>Hora</th>
          <th>Tipo de Evento</th>
          <th>Socio</th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="reserva in filteredReservas" :key="reserva.id">
          <td>{{ reserva.fechaEvento }}</td>
          <td>{{ reserva.horaEvento }}</td>
          <td>{{ reserva.tipoEvento }}</td>
          <td>{{ getSocioName(reserva.socioId) }}</td>
          <td>
            <button @click="editReserva(reserva)" class="text-green-700">Editar</button>
            <button @click="deleteReserva(reserva.id)" class="text-red-500">Eliminar</button>
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
      reservas: [],
      socios: [],
      currentReserva: { fechaEvento: "", horaEvento: "", tipoEvento: "", socioId: "" },
      searchQuery: "",
    };
  },
  computed: {
    filteredReservas() {
      return this.reservas.filter((reserva) =>
        reserva.tipoEvento.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },
  },
  methods: {
    async fetchReservas() {
      try {
        const response = await axios.get("http://localhost:3000/reservas");
        this.reservas = response.data;
      } catch (error) {
        console.error("Error al cargar las reservas:", error);
      }
    },
    async fetchSocios() {
      try {
        const response = await axios.get("http://localhost:3000/socios");
        this.socios = response.data;
      } catch (error) {
        console.error("Error al cargar los socios:", error);
      }
    },
    getSocioName(socioId) {
      const socio = this.socios.find((s) => s.id === socioId);
      return socio ? socio.name : "Desconocido";
    },
    async submitReserva() {
      if (this.currentReserva.id) {
        try {
          const response = await axios.put(
            `http://localhost:3000/reservas/${this.currentReserva.id}`,
            this.currentReserva
          );
          const index = this.reservas.findIndex((r) => r.id === this.currentReserva.id);
          this.reservas.splice(index, 1, response.data);
        } catch (error) {
          console.error("Error al actualizar la reserva:", error);
        }
      } else {
        try {
          const response = await axios.post("http://localhost:3000/reservas", this.currentReserva);
          this.reservas.push(response.data);
        } catch (error) {
          console.error("Error al agregar la reserva:", error);
        }
      }
      this.resetForm();
    },
    async deleteReserva(id) {
      try {
        await axios.delete(`http://localhost:3000/reservas/${id}`);
        this.reservas = this.reservas.filter((reserva) => reserva.id !== id);
      } catch (error) {
        console.error("Error al eliminar la reserva:", error);
      }
    },
    editReserva(reserva) {
      this.currentReserva = { ...reserva };
    },
    resetForm() {
      this.currentReserva = { fechaEvento: "", horaEvento: "", tipoEvento: "", socioId: "" };
    },
  },
  mounted() {
    this.fetchReservas();
    this.fetchSocios();
  },
};
</script>


<style>
body {
  font-family: Arial, sans-serif;
  background-color: #e2ebf5;
}
.text-green-700 {
  color: #059b50;
}
.text-red-500 {
  color: #ff4d4d;
}
table {
  border-collapse: collapse;
  width: 100%;
  margin-top: 20px;
}
th,
td {
  border: 1px solid #045737;
  text-align: left;
  padding: 8px;
}
thead {
  background-color: #e3f9e5;
}
</style>
