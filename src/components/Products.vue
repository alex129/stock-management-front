<script setup lang="ts">
import { ref, onMounted, reactive } from "vue";
import axios from "axios";

const baseUrl = "http://pdpaola.backend.test";

interface Product {
     id: number;
     name: string;
     quantity: number;
}

defineProps({
     msg: String,
});

const state = reactive<{ products: Product[], quantities: number[] }>({
     products: [],
     quantities: []
});

onMounted(() => {
     fetchData();
});

const fetchData = () => {
     const url = baseUrl + '/api/products';
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
     const url = baseUrl + '/api/movements';
     axios.post(url, {product_id: product.id, quantity}).then((res) => {
               console.log(res.data);
               fetchData();
          })
          .catch((err) => {
               console.log(err);
          });
};
</script>

<template>
     <div class="container">
          <h3>Products</h3>
          <table class="table table-bordered table-striped table-sm">
               <thead>
                    <tr>
                         <th>id</th>
                         <th>Name</th>
                         <th>Quantity</th>
                         <th></th>
                    </tr>
               </thead>
               <tbody>
                    <tr v-for="(product, index) in state.products" :key="product.id">
                         <td>{{ product.id }}</td>
                         <td>{{ product.name }}</td>
                         <td>{{ product.quantity }}</td>
                         <td>
                              <div class="d-flex gap-1">
                                   <input type="number" class="input-control" v-model="state.quantities[index]" />
                                   <button class="btn btn-sm btn-primary" @click="addMovemement(product, state.quantities[index])">Add stock movement</button>
                              </div>
                         </td>
                    </tr>
               </tbody>
          </table>
     </div>
</template>

<style scoped></style>
