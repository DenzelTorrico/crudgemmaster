<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head } from '@inertiajs/vue3';
</script>

<template>
    <Head title="Categoria" />

    <AuthenticatedLayout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">Categoria</h2>
        </template>

        <div>
    <table class="min-w-full bg-white border border-gray-300">
      <thead>
        <tr>
          <th class="py-2 px-4 border-b">ID</th>
          <th class="py-2 px-4 border-b">Nombre</th>
          <th class="py-2 px-4 border-b">Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="categoria in categorias" :key="categoria.id" class="hover:bg-gray-50">
          <td class="py-2 px-4 border-b">{{ categoria.id }}</td>
          <td class="py-2 px-4 border-b">
            <span v-if="!categoria.editando">{{ categoria.nombre }}</span>
            <input
              v-if="categoria.editando"
              v-model="categoria.nuevoNombre"
              placeholder="Nuevo nombre"
              required
              class="border rounded py-2 px-4 focus:outline-none focus:ring focus:border-blue-300"
            />
          </td>
          <td class="py-2 px-4 border-b">
            <button @click="eliminarCategoria(categoria.id)" class="text-red-500 hover:text-red-700">
              Eliminar
            </button>
            <button @click="editarCategoria(categoria)" class="text-blue-500 hover:text-blue-700">
              {{ categoria.editando ? 'Guardar' : 'Editar' }}
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <form @submit.prevent="agregarCategoria" class="mt-2 flex items-center space-x-2">
      <input
        v-model="nombreCategoria"
        placeholder="Nombre de categoría"
        required
        class="border rounded py-2 px-4 focus:outline-none focus:ring focus:border-blue-300"
      />
      <button type="submit" class="bg-blue-500 text-white py-2 px-4 rounded hover:bg-blue-700 focus:outline-none focus:ring focus:border-blue-300">
        Agregar Categoría
      </button>
    </form>
  </div>
    </AuthenticatedLayout>
</template>

<script>
import { ref } from 'vue';
import axios from 'axios';

export default {
  data() {
    return {
      categorias: [],
      nombreCategoria: '',
    };
  },
  methods: {
    async cargarCategorias() {
      try {
        const response = await axios.get('/api/categorias');
        this.categorias = response.data.map(categoria => ({ ...categoria, editando: false, nuevoNombre: categoria.nombre }));
      } catch (error) {
        console.error('Error al cargar las categorías:', error);
      }
    },
    async agregarCategoria() {
      try {
        const response = await axios.post('/api/categorias', { nombre: this.nombreCategoria });
        this.categorias.push(response.data);
        this.nombreCategoria = '';
      } catch (error) {
        console.error('Error al agregar la categoría:', error);
      }
    },
    async eliminarCategoria(id) {
      try {
        await axios.delete(`/api/categorias/${id}`);
        this.categorias = this.categorias.filter(categoria => categoria.id !== id);
      } catch (error) {
        console.error('Error al eliminar la categoría:', error);
      }
    },
    editarCategoria(categoria) {
      if (categoria.editando) {
        // Lógica para guardar cambios
        axios.put(`/api/categorias/${categoria.id}`, { nombre: categoria.nuevoNombre })
          .then(() => {
            categoria.nombre = categoria.nuevoNombre;
            categoria.editando = false;
          })
          .catch(error => {
            console.error('Error al actualizar la categoría:', error);
          });
      } else {
        // Habilitar la edición
        categoria.editando = true;
      }
    },
  },
  mounted() {
    this.cargarCategorias();
  },
};
</script>
