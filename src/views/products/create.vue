<script setup lang="ts">
import { onMounted, ref } from "vue";
import { useRouter } from "vue-router";
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
const description = ref("");
const category_id = ref("");
const price = ref("");
const stock = ref("");

const categories = ref<Categories[]>([]);
const errors = ref<Errors>({});

const router = useRouter();

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
});

const storeProduct = async () => {
  const formData = new FormData();
  formData.append("name", name.value);
  formData.append("description", description.value);
  formData.append("category_id", category_id.value); 
  formData.append("price", price.value);
  formData.append("stock", stock.value);

  try {
    await Api.post("/api/products", formData);
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
        <div class="card border-0 rounded-3 shadow">
          <div class="card-body">
            <form @submit.prevent="storeProduct">
              <div class="mb-3">
                <label class="form-label fw-bold">Name</label>
                <input
                  type="text"
                  v-model="name"
                  class="form-control"
                  placeholder="Name of product"
                />
                <div v-if="errors.name" class="alert alert-danger mt-2">
                  {{ errors.name[0] }}
                </div>
              </div>

              <div class="mb-3">
                <label class="form-label fw-bold">Category</label>
                <select v-model="category_id" class="form-control">
                  <option value="" disabled>Choose Categories</option>
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

              <div class="mb-3">
                <label class="form-label fw-bold">Description</label>
                <textarea
                  v-model="description"
                  class="form-control"
                  rows="4"
                  placeholder="Description Product"
                ></textarea>
                <div v-if="errors.description" class="alert alert-danger mt-2">
                  {{ errors.description[0] }}
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
                      placeholder="Stock Product"
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
                  class="btn btn-md btn-primary rounded-4 shadow border-0"
                >
                  Save
                </button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
