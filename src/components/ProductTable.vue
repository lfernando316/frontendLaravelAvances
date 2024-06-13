<template>
  <div class="container mx-auto p-4">
    <h1 class="text-2xl font-bold mb-4">Lista de productos</h1>
    <table class="min-w-full divide-y divide-gray-200">
      <thead class="bg-gray-50">
        <tr>
          <th
            scope="col"
            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
          >
            Nombre
          </th>
          <th
            scope="col"
            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
          >
            Precio
          </th>
          <th
            scope="col"
            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
          >
            Acción
          </th>
        </tr>
      </thead>
      <tbody class="bg-white divide-y divide-gray-200">
        <tr v-for="product in products" :key="product.id">
          <td
            class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900"
          >
            {{ product.nombre }}
          </td>
          <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
            {{ product.precio }}
          </td>
          <td
            class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium"
          >
            <button
              @click="editProduct(product)"
              class="text-indigo-600 hover:text-indigo-900"
            >
              Editar
            </button>
            <button
              @click="confirmDeleteProduct(product.id, product.nombre)"
              class="text-red-600 hover:text-red-900 ml-2"
            >
              Eliminar
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import { useProductStore } from "../stores/product";
import { mapActions, mapState } from "pinia";
import axios from "axios";
import Swal from "sweetalert2";

export default {
  name: "ProductTable",
  computed: {
    ...mapState(useProductStore, ["products"]),
  },
  methods: {
    ...mapActions(useProductStore, ["fetchProducts"]),
    async deleteProduct(productId, productName) {
      try {
        // Hacer la solicitud DELETE a la API
        await axios.delete(`http://localhost:8000/api/products/${productId}`);
        Swal.fire(
          "Eliminado",
          `El producto ${productName} ha sido eliminado`,
          "success"
        );
        // Actualizar la lista de productos después de la eliminación
        this.fetchProducts();
      } catch (error) {
        console.error(
          `Error al eliminar el producto con ID ${productId}:`,
          error
        );
        let errorMessage =
          "Hubo un problema al eliminar el producto. Intente de nuevo más tarde.";
        if (
          error.response &&
          error.response.data &&
          error.response.data.message
        ) {
          errorMessage = error.response.data.message;
        }
        Swal.fire({
          icon: "error",
          title: "Error",
          text: errorMessage,
        });
      }
    },
    confirmDeleteProduct(productId, productName) {
      Swal.fire({
        title: `¿Estás seguro de eliminar el producto ${productName}?`,
        text: "Esta acción no se puede revertir",
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Sí, eliminar",
        cancelButtonText: "Cancelar",
      }).then((result) => {
        if (result.isConfirmed) {
          this.deleteProduct(productId, productName);
        }
      });
    },
    editProduct(product) {
      // Llamar a la acción para cargar los detalles del producto para editar
      // this.$store.productStore.setSelectedProduct(product);
      // Emitir un evento para abrir la modal de edición en Home.vue
      this.$emit("edit-product", product);
    },
  },
  mounted() {
    this.fetchProducts();
  },
};
</script>
