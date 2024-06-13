<template>
  <div class="p-6">
    <h1 class="text-2xl font-bold mb-4">Home</h1>

    <!-- Botón para abrir la modal -->
    <button
      @click="openModal"
      class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded my-2"
    >
      Crear Producto
    </button>

    <!-- Modal para crear producto -->
    <div
      class="fixed z-10 inset-0 overflow-y-auto"
      :class="{ hidden: !isModalOpen }"
      aria-labelledby="modal-title"
      role="dialog"
      aria-modal="true"
    >
      <div
        class="flex items-center justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0"
      >
        <!-- Overlay oscuro -->
        <div
          class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity"
          aria-hidden="true"
        ></div>

        <!-- Contenido de la modal -->
        <span
          class="hidden sm:inline-block sm:align-middle sm:h-screen"
          aria-hidden="true"
          >&#8203;</span
        >
        <div
          class="inline-block align-bottom bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full"
          role="dialog"
          aria-modal="true"
          aria-labelledby="modal-headline"
        >
          <div class="bg-white px-4 pt-5 pb-4 sm:p-6 sm:pb-4">
            <div class="sm:flex sm:items-start">
              <!-- <div
                class="mx-auto flex-shrink-0 flex items-center justify-center h-12 w-12 rounded-full bg-indigo-100 sm:mx-0 sm:h-10 sm:w-10"
              > -->
              <!-- Icono o imagen de creación -->
              <!-- <svg
                  class="h-6 w-6 text-indigo-600"
                  xmlns="http://www.w3.org/2000/svg"
                  fill="none"
                  viewBox="0 0 24 24"
                  stroke="currentColor"
                  aria-hidden="true"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M12 6v6m0 0v6m0-6h6m-6 0H6"
                  />
                </svg>
              </div> -->
              <div class="mt-3 text-center sm:mt-0 sm:ml-4 sm:text-left">
                <h3
                  class="text-lg leading-6 font-medium text-gray-900"
                  id="modal-headline"
                >
                  {{ modalTitle }}
                </h3>
                <div class="mt-2">
                  <form>
                    <div class="mb-4">
                      <label
                        for="nombre"
                        class="block text-sm font-medium text-gray-700"
                        >Nombre</label
                      >
                      <input
                        type="text"
                        id="nombre"
                        v-model="newProduct.nombre"
                        autocomplete="off"
                        required
                        class="mt-1 px-3 py-2 block w-full shadow-sm sm:text-sm border-gray-300 rounded-md focus:ring-indigo-500 focus:border-indigo-500"
                      />
                    </div>
                    <div class="mb-4">
                      <label
                        for="precio"
                        class="block text-sm font-medium text-gray-700"
                        >Precio</label
                      >
                      <input
                        type="number"
                        id="precio"
                        v-model.number="newProduct.precio"
                        autocomplete="off"
                        required
                        class="mt-1 px-3 py-2 block w-full shadow-sm sm:text-sm border-gray-300 rounded-md focus:ring-indigo-500 focus:border-indigo-500"
                      />
                    </div>
                    <div class="mt-4">
                      <button
                        v-if="modalTitle == 'Editar Producto'"
                        @click="editProductApi"
                        class="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
                      >
                        Editar cambios
                      </button>
                      <button
                        v-else
                        @click="createProduct"
                        class="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
                      >
                        Guardar
                      </button>
                      <button
                        type="button"
                        @click="closeModal"
                        class="ml-3 inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-gray-700 bg-gray-200 hover:bg-gray-300 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-500"
                      >
                        Cancelar
                      </button>
                    </div>
                  </form>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <ProductTable @edit-product="editProduct" />
  </div>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
import Swal from "sweetalert2";
import ProductTable from "../components/ProductTable.vue";
import { useProductStore } from "../stores/product";
import { mapActions } from "pinia";
// import { fetchProducts } from "../components/ProductTable.vue";

const { fetchProducts } = useProductStore();

// Estado reactivo para controlar la apertura de la modal
const isModalOpen = ref(false);

// Estado para el título de la modal (crear o editar)
const modalTitle = ref("Crear Nuevo Producto");

// Datos del nuevo producto
const newProduct = ref({
  nombre: "",
  precio: null,
});

// Función para abrir la modal
const openModal = () => {
  isModalOpen.value = true;
};

// Función para cerrar la modal y limpiar los datos del formulario
const closeModal = () => {
  isModalOpen.value = false;
  newProduct.value.nombre = "";
  newProduct.value.precio = null;
  modalTitle.value = "Crear Nuevo Producto";
};

// Función para crear un nuevo producto
const createProduct = async () => {
  event.preventDefault();
  try {
    await axios.post("http://localhost:8000/api/products", {
      nombre: newProduct.value.nombre,
      precio: newProduct.value.precio,
    });

    // Mostrar mensaje de éxito
    Swal.fire({
      icon: "success",
      title: "Producto creado",
      text: `El producto "${newProduct.value.nombre}" ha sido creado correctamente.`,
    });
    // Cerrar la modal después de crear el producto
    fetchProducts();
    closeModal();
  } catch (error) {
    console.error("Error al crear el producto:", error);
    // Mostrar mensaje de error
    Swal.fire({
      icon: "error",
      title: "Error",
      text: "Hubo un problema al crear el producto. Intente de nuevo más tarde.",
    });
  }
};

const editProductApi = async () => {
  event.preventDefault();
  try {
    await axios.put(
      `http://localhost:8000/api/products/${newProduct.value.id}`,
      {
        nombre: newProduct.value.nombre,
        precio: newProduct.value.precio,
      }
    );

    // Mostrar mensaje de éxito
    Swal.fire({
      icon: "success",
      title: "Producto editado",
      text: `El producto "${newProduct.value.nombre}" ha sido editado correctamente.`,
    });
    // Cerrar la modal después de crear el producto
    fetchProducts();
    closeModal();
  } catch (error) {
    console.error("Error al editar el producto:", error);
    // Mostrar mensaje de error
    Swal.fire({
      icon: "error",
      title: "Error",
      text: "Hubo un problema al editar el producto. Intente de nuevo más tarde.",
    });
  }
};

// Estado para el producto en edición
const editedProduct = ref({
  id: null,
  nombre: "",
  precio: null,
});

const editProduct = (product) => {
  console.log(product, "holaa");

  newProduct.value.id = product.id;
  newProduct.value.nombre = product.nombre;
  newProduct.value.precio = product.precio;
  modalTitle.value = "Editar Producto";
  openModal();
};
</script>
