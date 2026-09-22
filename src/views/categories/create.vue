<script setup lang="ts">
import { ref } from "vue";
import { useRouter } from "vue-router";
import Api from "../../api";

interface Errors {
  name?: string[];
}

const name = ref("");

const errors = ref<Errors>({});

const router = useRouter();

const storeCategory = async () => {
  const formData = new FormData();
  formData.append("name", name.value);

  try {
    await Api.post("api/category", formData);
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
            <form @submit.prevent="storeCategory">
              <div class="mb-3">
                <label class="form-label fw-bold">Name</label>
                <input
                  type="text"
                  v-model="name"
                  class="form-control"
                  placeholder="Name of category"
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
