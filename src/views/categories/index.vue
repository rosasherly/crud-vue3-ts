<script setup lang="ts">
import { ref, onMounted } from "vue";
import Api from "../../api";

interface Category {
  id: number;
  name: string;
}

const categories = ref<Category[]>([]);
const fetchDataCategories = async () => {
  try {
    const response = await Api.get("/api/category/");
    categories.value = response.data.data || response.data;
  } catch (error) {
    console.error("Error fetching categories:", error);
  }
};

onMounted(() => {
  fetchDataCategories();
});

const deleteCategory = async (id: number) => {
  if (confirm("Apakah Anda yakin ingin menghapus kategori ini?")) {
    try {
      await Api.delete(`/api/category/${id}`);
      categories.value = categories.value.filter((category) => category.id !== id);
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
          to="/categories/create"
          class="btn btn-md btn-success rounded-5 shadow border-0 mb-3"
        >
          ADD NEW CATEGORY
        </router-link>
        <div class="card border-0 rounded-5 shadow">
          <div class="card-body">
            <table class="table table-bordered">
              <thead class="bg-dark text-white text-center">
                <tr>
                  <th scope="col">Name</th>
                  <th scope="col" style="width: 15%">Actions</th>
                </tr>
              </thead>
              <tbody>
                <tr v-if="categories.length === 0">
                  <td colspan="2" class="text-center">
                    <div class="alert alert-danger mb-0">No Data Available</div>
                  </td>
                </tr>
                <tr v-for="category in categories" :key="category.id">
                  <td>{{ category.name }}</td>
                  <td class="text-center">
                    <router-link
                      :to="`/categories/edit/${category.id}`"
                      class="btn btn-sm btn-primary rounded-5 shadow border-0 me-2"
                      >EDIT</router-link
                    >
                    <button
                      @click="deleteCategory(category.id)"
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
