<script setup lang="ts">
import { ref, onMounted } from "vue";
import Api from "../../api";

interface Product {
  id: number;
  name: string;
  category_id: number;
  category: string;
  description?: string;
  price: number;
  stock: number;
}

const products = ref<Product[]>([]);
const fetchDataProducts = async () => {
  try {
    const response = await Api.get("/api/products");
    products.value = response.data.data || response.data;
  } catch (error) {
    console.error("Error fetching products:", error);
  }
};

onMounted(() => {
  fetchDataProducts();
});

const deleteProduct = async (id: number) => {
  if (confirm("Apakah Anda yakin ingin menghapus produk ini?")) {
    try {
      await Api.delete(`/api/products/${id}`);
      products.value = products.value.filter((product) => product.id !== id);
    } catch (error) {
      console.error("Error deleting category:", error);
    }
  }
};
</script>
<template>
  <div class="container mt-5 mb-5">
    <div class="row">
      <div class="col-md-12">
        <router-link
          to="/products/create"
          class="btn btn-md btn-success rounded-5 shadow border-0 mb-3"
        >
          ADD NEW PRODUCT
        </router-link>
        <div class="card border-0 rounded-5 shadow">
          <div class="card-body">
            <table class="table table-bordered">
              <thead class="bg-dark text-white text-center">
                <tr>
                  <th scope="col">Name</th>
                  <th scope="col">Description</th>
                  <th scope="col">Category</th>
                  <th scope="col">Price</th>
                  <th scope="col">Stock</th>
                  <th scope="col" style="width: 15%">Actions</th>
                </tr>
              </thead>
              <tbody>
                <tr v-if="products.length === 0">
                  <td colspan="6" class="text-center">
                    <div class="alert alert-danger mb-0">No Data Available</div>
                  </td>
                </tr>
                <tr v-for="product in products" :key="product.id">
                  <td>{{ product.name }}</td>
                  <td>{{ product.description }}</td>
                  <td>{{ product.category }}</td>
                  <td>{{ product.price }}</td>
                  <td>{{ product.stock }}</td>
                  <td class="text-center">
                    <router-link
                      :to="`/products/edit/${product.id}`"
                      class="btn btn-sm btn-primary rounded-5 shadow border-0 me-2"
                      >EDIT</router-link
                    >
                    <button
                      @click="deleteProduct(product.id)"
                      class="btn btn-sm btn-danger rounded-5 shadow border-0"
                    >
                      DELETE
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
