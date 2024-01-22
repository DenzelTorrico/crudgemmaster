<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head } from '@inertiajs/vue3';
</script>
<template>
    <Head title="Productos" />

<AuthenticatedLayout>
    <template #header>
        <h2 class="font-semibold text-xl text-gray-800 leading-tight">Productos</h2>
    </template>
    <div>
    <table class="min-w-full bg-white border border-gray-300">
      <thead>
        <tr>
          <th class="py-2 px-4 border-b">ID</th>
          <th class="py-2 px-4 border-b">Nombre</th>
          <th class="py-2 px-4 border-b">Precio</th>
          <th class="py-2 px-4 border-b">Stock</th>
          <th class="py-2 px-4 border-b">Categoría</th>
          <th class="py-2 px-4 border-b">Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="producto in productos" :key="producto.id" class="hover:bg-gray-50">
          <td class="py-2 px-4 border-b">{{ producto.id }}</td>
          <td class="py-2 px-4 border-b">
            <span v-if="!producto.editando">{{ producto.nombre }}</span>
            <input
              v-if="producto.editando"
              v-model="producto.nuevoNombre"
              placeholder="Nuevo nombre"
              required
              class="border rounded py-2 px-4 focus:outline-none focus:ring focus:border-blue-300"
            />
          </td>
          <td class="py-2 px-4 border-b">
            <span v-if="!producto.editando">{{ producto.precio }}</span>
            <input
              v-if="producto.editando"
              v-model="producto.nuevoPrecio"
              placeholder="Nuevo precio"
              required
              class="border rounded py-2 px-4 focus:outline-none focus:ring focus:border-blue-300"
            />
          </td>
          <td class="py-2 px-4 border-b">
            <span v-if="!producto.editando">{{ producto.stock }}</span>
            <input
              v-if="producto.editando"
              v-model="producto.nuevoStock"
              placeholder="Nuevo stock"
              required
              class="border rounded py-2 px-4 focus:outline-none focus:ring focus:border-blue-300"
            />
          </td>
          <td class="py-2 px-4 border-b">
            <span v-if="!producto.editando">{{ obtenerNombreCategoria(producto.categoria_id) }}</span>
            <select
              v-if="producto.editando"
              v-model="producto.nuevaCategoria"
              class="border rounded py-2 px-4 focus:outline-none focus:ring focus:border-blue-300"
            >
              <option v-for="categoria in categorias" :key="categoria.id" :value="categoria.id">{{ categoria.nombre }}</option>
            </select>
          </td>
          <td class="py-2 px-4 border-b">
            <button @click="eliminarProducto(producto.id)" class="text-red-500 hover:text-red-700">
              Eliminar
            </button>
            <button @click="editarProducto(producto)" class="text-blue-500 hover:text-blue-700">
              {{ producto.editando ? 'Guardar' : 'Editar' }}
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <form @submit.prevent="agregarProducto" class="mt-2 flex items-center space-x-2">
      <input
        v-model="nombreProducto"
        placeholder="Nombre de producto"
        required
        class="border rounded py-2 px-4 focus:outline-none focus:ring focus:border-blue-300"
      />
      <input
        v-model="precioProducto"
        placeholder="Precio"
        required
        class="border rounded py-2 px-4 focus:outline-none focus:ring focus:border-blue-300"
      />
      <input
        v-model="stockProducto"
        placeholder="Stock"
        required
        class="border rounded py-2 px-4 focus:outline-none focus:ring focus:border-blue-300"
      />
      <select
        v-model="categoriaProducto"
        class="border rounded py-2 px-4 focus:outline-none focus:ring focus:border-blue-300"
      >
        <option v-for="categoria in categorias" :key="categoria.id" :value="categoria.id">{{ categoria.nombre }}</option>
      </select>
      <button type="submit" class="bg-blue-500 text-white py-2 px-4 rounded hover:bg-blue-700 focus:outline-none focus:ring focus:border-blue-300">
        Agregar Producto
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
      productos: [],
      categorias: [],
      nombreProducto: '',
      precioProducto: '',
      stockProducto: '',
      categoriaProducto: null,
    };
  },
  methods: {
    async cargarProductos() {
      try {
        const response = await axios.get('/api/productos');
        this.productos = response.data.map(producto => ({ ...producto, editando: false, nuevoNombre: producto.nombre, nuevoPrecio: producto.precio, nuevoStock: producto.stock, nuevaCategoria: producto.categoria_id }));
      } catch (error) {
        console.error('Error al cargar los productos:', error);
      }
    },
    async cargarCategorias() {
      try {
        const response = await axios.get('/api/categorias');
        this.categorias = response.data;
      } catch (error) {
        console.error('Error al cargar las categorías:', error);
      }
    },
    async agregarProducto() {
      try {
        const response = await axios.post('/api/productos', {
          nombre: this.nombreProducto,
          precio: this.precioProducto,
          stock: this.stockProducto,
          categoria_id: this.categoriaProducto,
        });
        this.productos.push(response.data);
        this.nombreProducto = '';
        this.precioProducto = '';
        this.stockProducto = '';
        this.categoriaProducto = null;
      } catch (error) {
        console.error('Error al agregar el producto:', error);
      }
    },
    async eliminarProducto(id) {
      try {
        await axios.delete(`/api/productos/${id}`);
        this.productos = this.productos.filter(producto => producto.id !== id);
      } catch (error) {
        console.error('Error al eliminar el producto:', error);
      }
    },
    editarProducto(producto) {
      if (producto.editando) {
        // Lógica para guardar cambios
        axios.put(`/api/productos/${producto.id}`, {
          nombre: producto.nuevoNombre,
          precio: producto.nuevoPrecio,
          stock: producto.nuevoStock,
          categoria_id: producto.nuevaCategoria,
        })
          .then(() => {
            producto.nombre = producto.nuevoNombre;
            producto.precio = producto.nuevoPrecio;
            producto.stock = producto.nuevoStock;
            producto.categoria_id = producto.nuevaCategoria;
            producto.editando = false;
          })
          .catch(error => {
            console.error('Error al guardar los cambios:', error);
          });
      } else {
        // Habilitar la edición
        producto.editando = true;
      }
    },
    obtenerNombreCategoria(idCategoria) {
      const categoria = this.categorias.find(c => c.id === idCategoria);
      return categoria ? categoria.nombre : 'Sin categoría';
    },
  },
  mounted() {
    this.cargarProductos();
    this.cargarCategorias();
  },
};
  </script>
  