<template>
  <div id="app" class="min-h-screen bg-gray-100 text-gray-900">
    <header class="bg-blue-600 text-white p-4 text-center">
      <div id="container3">
      <img src="./assets/log.png" alt="Club de Tenis" class="w-16 mx-auto mb-2" />
      <h1 class="text-2xl font-bold text-green-700" >Gestión del Club de Tenis</h1>
    </div>
    </header>

    <main class="p-4 space-y-8">
      <section>
        <SocioTable />
      </section>
      <section>
        <DeudaTable />
      </section>
      <section>
        <ReservaTable />
      </section>
    </main>
  </div>
</template>

<script>
import SocioTable from "./components/SocioTable.vue";
import DeudaTable from "./components/DeudaTable.vue";
import ReservaTable from "./components/ReservaTable.vue";

import './main.css'
export default {
  components: {
    SocioTable,
    DeudaTable,
    ReservaTable,
  },
};
</script>

<style>
#container3 img{
  display:block;
  margin:0 auto;
  text-align: center;
}
/* General styles for green tones */
body {
  font-family: Arial, sans-serif;
  background-color: #e2ebf5;
}
.text-green-700 { color: #058042; }
.ring-green-500 {color: #375704;}
.bg-green-600{color:#375704}
.td-background-green{columns: rgba(8, 228, 8, 0.774);}

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
