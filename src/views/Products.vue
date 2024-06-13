<template>
  <div>
    <h1>Products</h1>
    <button @click="loadProducts">Load Products</button>
    <ul>
      <li v-for="product in products" :key="product.id">
        {{ product.nombre }} - {{ product.precio }}
      </li>
    </ul>
  </div>
</template>

<script>
import { useProductStore } from "../stores/product";
import { mapActions, mapState } from "pinia";

export default {
  name: "Products",
  computed: {
    ...mapState(useProductStore, ["products"]),
  },
  methods: {
    ...mapActions(useProductStore, ["fetchProducts"]),
    loadProducts() {
      this.fetchProducts();
    },
  },
};
</script>
