<script setup lang="ts">
import { ref, onMounted } from "vue";
import { useRoute, useRouter } from "vue-router";
import Api from "../../api";

interface Categories {
  id: number;
  name: string;
}

interface Errors {
  name?: string[];
  category_id?: string[];
  description?: string[];
  price?: string[];
  stock?: string[];
}

const name = ref("");
const category_id = ref("");
const description = ref("");
const price = ref("");
const stock = ref("");

const errors = ref<Errors>({});
const categories = ref<Categories[]>([]);

const route = useRoute();
const router = useRouter();

const fetchDetailProduct = async () => {
  try {
    const response = await Api.get(`/api/products/${route.params.id}`);

    name.value = response.data.data.name;
    category_id.value = response.data.data.category_id;
    description.value = response.data.data.description;
    price.value = response.data.data.price;
    stock.value = response.data.data.stock;
  } catch (error) {
    console.error("Error fetching product:", error);
  }
};

const fetchCategories = async () => {
  try {
    const response = await Api.get("/api/category");
    categories.value = response.data.data || response.data;
  } catch (error) {
    console.error("Gagal mengambil category data:", error);
  }
};

onMounted(() => {
  fetchCategories();
  fetchDetailProduct();
});

const updateProduct = async () => {
  const formData = new FormData();

  formData.append("name", name.value);
  formData.append("category_id", category_id.value);
  formData.append("description", description.value);
  formData.append("price", price.value);
  formData.append("stock", stock.value);
  formData.append("_method", "PUT");

  try {
    await Api.post(`/api/products/${route.params.id}`, formData);
    router.push("/products");
  } catch (error: any) {
    if (error.response && error.response.data.errors) {
      errors.value = error.response.data.errors;
    } else if (error.response && error.response.data) {
      errors.value = error.response.data;
    }
  }
};
</script>
<template>
  <div class="container mt-5">
    <div class="row">
      <div class="col-md-12">
        <div class="card border-0 rouded-3 shadow">
          <div class="card-body">
            <form @submit.prevent="updateProduct">
              <div class="mb-3">
                <label class="form-label fw-bold">Name</label>
                <input
                  type="text"
                  v-model="name"
                  class="form-control"
                  placeholder="Name of Product"
                />
                <div v-if="errors.name" class="alert alert-danger mt-2">
                  {{ errors.name[0] }}
                </div>
              </div>
              <div class="mb-3">
                <label class="form-label fw-bold">Description</label>
                <input
                  type="text"
                  v-model="description"
                  class="form-control"
                  placeholder="Name of Product"
                />
                <div v-if="errors.description" class="alert alert-danger mt-2">
                  {{ errors.description[0] }}
                </div>
              </div>
              <div class="mb-3">
                <label class="form-label fw-bold">Category</label>
                <select v-model="category_id" class="form-control">
                  <option value="" disabled>Choose Category</option>
                  <option
                    v-for="cat in categories"
                    :key="cat.id"
                    :value="cat.id"
                  >
                    {{ cat.name }}
                  </option>
                </select>
                <div v-if="errors.category_id" class="alert alert-danger mt-2">
                  {{ errors.category_id[0] }}
                </div>
              </div>
              <div class="row">
                <div class="col-md-6">
                  <div class="mb-3">
                    <label class="form-label fw-bold">Price</label>
                    <input
                      type="number"
                      v-model="price"
                      class="form-control"
                      placeholder="Price Product"
                    />
                    <div v-if="errors.price" class="alert alert-danger mt-2">
                      {{ errors.price[0] }}
                    </div>
                  </div>
                </div>
                <div class="col-md-6">
                  <div class="mb-3">
                    <label class="form-label fw-bold">Stock</label>
                    <input
                      type="number"
                      v-model="stock"
                      class="form-control"
                      placeholder="Stock Product "
                    />
                    <div v-if="errors.stock" class="alert alert-danger mt-2">
                      {{ errors.stock[0] }}
                    </div>
                  </div>
                </div>
              </div>
              <div>
                <router-link
                  to="/products"
                  class="btn btn-md btn-secondary rounded-3 shadow border-0 mx-2"
                >
                  Cancel
                </router-link>
                <button
                  type="submit"
                  class="btn btn-md btn-primary rounded-5 shadow border-0"
                >
                  Update
                </button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
