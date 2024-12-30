<template>
  <div class="bg-white rounded-lg shadow p-4">
    <h2 class="text-lg font-bold mb-4 text-green-700">Gestión de Socios</h2>

    <!-- Formulario de búsqueda -->
    <form @submit.prevent class="flex items-center mb-4">
      <input
        v-model="searchQuery"
        placeholder="Buscar por nombre"
        class="flex-grow border p-2 rounded focus:outline-none focus:ring-2 focus:ring-green-500"
      />
    </form>

    <!-- Tabla de socios -->
    <table class="w-full text-left border-collapse bg-green-50">
      <thead>
        <tr class="bg-green-600 text-white ">
          <th class="border-b p-2">Nombre</th>
          <th class="border-b p-2">Email</th>
          <th class="border-b p-2">Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="socio in filteredSocios"
          :key="socio.id"
          class="odd:bg-green-100 even:bg-white"
        >
          <td class="border-b p-2">{{ socio.name }}</td>
          <td class="border-b p-2">{{ socio.email }}</td>
          <td class="border-b p-2 space-x-2">
            <button
              @click="editSocio(socio)"
              class="text-lg font-bold mb-4 text-green-700"
            >
              Editar
            </button>
            <button
              @click="deleteSocio(socio.id)"
              class="text-lg font-bold mb-4 text-green-700"
            >
              Eliminar
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Formulario de creación/actualización -->
    <form
      @submit.prevent="handleSubmit"
      class="mt-4 bg-green-50 p-4 rounded shadow"
    >
      <h3 class="text-lg font-semibold text-green-700 mb-2">
        {{ formSocio.id ? "Actualizar Socio" : "Agregar Socio" }}
      </h3>
      <input
        v-model="formSocio.name"
        placeholder="Nombre del socio"
        class="border p-2 rounded mb-2 w-full focus:outline-none focus:ring-2 focus:ring-green-500"
        required
      />
      <input
        v-model="formSocio.email"
        placeholder="Correo electrónico"
        class="border p-2 rounded mb-2 w-full focus:outline-none focus:ring-2 focus:ring-green-500"
        required
      />
      <button
        type="submit"
        class="text-lg font-bold mb-4 text-green-700"
      >
        {{ formSocio.id ? "Actualizar" : "Agregar" }}
      </button>
    </form>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      socios: [],
      formSocio: { name: "", email: "" },
      searchQuery: "",
    };
  },
  computed: {
    filteredSocios() {
      return this.socios.filter((socio) =>
        socio.name.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },
  },
  methods: {
    async fetchSocios() {
      const response = await axios.get("http://localhost:3000/socios");
      this.socios = response.data;
    },
    async handleSubmit() {
      if (this.formSocio.id) {
        // Actualizar
        await axios.put(
          `http://localhost:3000/socios/${this.formSocio.id}`,
          this.formSocio
        );
        const index = this.socios.findIndex(
          (socio) => socio.id === this.formSocio.id
        );
        this.socios.splice(index, 1, { ...this.formSocio });
      } else {
        // Crear
        const response = await axios.post(
          "http://localhost:3000/socios",
          this.formSocio
        );
        this.socios.push(response.data);
      }
      this.resetForm();
    },
    resetForm() {
      this.formSocio = { name: "", email: "" };
    },
    editSocio(socio) {
      this.formSocio = { ...socio };
    },
    async deleteSocio(id) {
      await axios.delete(`http://localhost:3000/socios/${id}`);
      this.socios = this.socios.filter((socio) => socio.id !== id);
    },
  },
  mounted() {
    this.fetchSocios();
  },
};
</script>

<style>
/* General styles for green tones */
body {
  font-family: Arial, sans-serif;
  background-color: #e2ebf5;
}


table th{
  border: 1px solid #375704;
}
table td {
  border: 1px solid #045737;
}

button {
  transition: background-color 0.2s;
}

input:focus {
  outline: none;
}
</style>

