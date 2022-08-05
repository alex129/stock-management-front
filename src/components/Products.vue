<script setup lang="ts">
import { ref, onMounted, reactive, computed } from "vue";
import axios from "axios";

const baseUrl = process.env.BASE_URL;

interface Product {
     id: number;
     name: string;
     quantity: number;
}

defineProps({
     msg: String,
});

const searchValue = ref<String>("");

const state = reactive<{ products: Product[]; quantities: number[] }>({
     products: [],
     quantities: [],
});

const products = computed<Product[]>(() => {
  return state.products.filter(p => p.name.indexOf(searchValue.value.toString()) >= 0 || p.id === Number.parseInt(searchValue.value.toString()));
})

onMounted(() => {
     fetchData();
});

const fetchData = () => {
     const url = baseUrl + "/api/products";
     axios.get(url)
          .then((res) => {
               console.log(res.data);
               state.products = res.data;
          })
          .catch((err) => {
               console.log(err);
          });
};

const addMovemement = (product: Product, quantity) => {
     const url = baseUrl + "/api/movements";
     axios.post(url, { product_id: product.id, quantity })
          .then((res) => {
               console.log(res.data);
               fetchData();
          })
          .catch((err) => {
               console.log(err);
          });
};

const searchProduct = (event) => {

};
</script>

<template>
     <div class="container">
          <h1>Simple stock management</h1>
          <h3>Products</h3>
          <div class="search mb-4 mt-4">
               <input type="text" class="form-control form-control-sm text-center" placeholder="Buscar" v-model="searchValue" />
          </div>
          <div class="products-grid">
               <div class="card" v-for="(product, index) in products" :key="product.id">
                    <div class="card-header">Product {{ product.id }}</div>
                    <div class="card-body">
                         <h6>Name</h6>
                         <p>{{ product.name }}</p>
                         <h6>Quantity</h6>
                         <p>{{ product.quantity }}</p>
                         <div class="input-group">
                              <input type="number" class="form-control form-control-sm text-center" v-model="state.quantities[index]" />
                              <button class="btn btn-outline-dark" @click="addMovemement(product, state.quantities[index])">Add stock</button>
                         </div>
                    </div>
               </div>
          </div>
     </div>
</template>

<style scoped>
.products-grid {
     display: grid;
     margin: 0 auto;
     gap: 1rem;
}

@media (min-width: 900px) {
  .products-grid { grid-template-columns: repeat(4, 1fr); }
}

@media (min-width: 600px) {
  .products-grid { grid-template-columns: repeat(3, 1fr); }
}
</style>
