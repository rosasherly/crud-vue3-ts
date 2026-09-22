<script setup lang="ts">
import { onMounted, ref } from "vue";
import { useRoute, useRouter } from "vue-router";
import Api from "../../api";

interface Errors {
  name?: string[];
}

const name = ref("");

const errors = ref<Errors>({});

const route = useRoute();
const router = useRouter();

const fetchDetailCategory = async () => {
  try {
    const response = await Api.get(`/api/category/${route.params.id}`);

    name.value = response.data.data.name;
  } catch (error) {
    console.error("Error fetching category:", error);
  }
};

onMounted(() => {
  fetchDetailCategory();
});

const updateCategory = async () => {
  const formData = new FormData();

  formData.append("name", name.value);
  formData.append("_method", "PUT");

  try {
    await Api.post(`/api/category/${route.params.id}`, formData);
    router.push("/categories");
  } catch (error: any) {
    if (error.response && error.response.data.errors) {
      errors.value = error.response.data.errors;
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
            <form @submit.prevent="updateCategory">
              <div class="mb-3">
                <label class="form-label fw-bold">Name</label>
                <input
                  type="text"
                  v-model="name"
                  class="form-control"
                  placeholder="Name of Category"
                />
                <div v-if="errors.name" class="alert alert-danger mt-2">
                  {{ errors.name[0] }}
                </div>
              </div>
              <div>
                <router-link
                  to="/categories"
                  class="btn btn-md btn-secondary rounded-3 shadow border-0 mx-2"
                >
                  Cancel
                </router-link>
                <button
                  type="submit"
                  class="btn btn-md btn-primary rounded-3 shadow border-0"
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
