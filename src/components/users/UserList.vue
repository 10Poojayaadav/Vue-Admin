<template>
  <div class="container mt-5">
    <div class="card">
      <div
        class="card-header d-flex justify-content-between align-items-center"
      >
        <h5 class="mb-0">Product List</h5>
        <router-link to="/users/create" class="btn btn-primary btn-sm"
          >Add Product</router-link
        >
      </div>
      <div class="card-body">
        <table
          id="productsTable"
          class="table table-striped table-bordered"
          style="width: 100%"
        >
          <thead>
            <tr>
              <th>ID</th>
              <th>Product Name</th>
              <th>Price</th>
              <th>Image</th>
              <th>Stock</th>
              <th>Description</th>
              <th>Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="product in products" :key="product.id">
              <td>{{ product.id }}</td>
              <td>{{ product.product_name }}</td>
              <td>{{ product.price }}</td>
              <td>
                <img
                  :src="product.image_url"
                  alt="Product Image"
                  style="width: 50px; height: auto"
                />
              </td>
              <td>{{ product.stock }}</td>
              <td>{{ product.description }}</td>
              <td>
                <button class="btn btn-success btn-sm me-2">Edit</button>
                <button class="btn btn-info btn-sm me-2">View</button>
                <button class="btn btn-danger btn-sm">Delete</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import $ from "jquery";
import "datatables.net-bs5";

export default {
  name: "ProductList",
  data() {
    return {
      products: [],
    };
  },
  mounted() {
    this.fetchProducts();
  },
  methods: {
    async fetchProducts() {
      try {
        const token = localStorage.getItem("token");
        const response = await axios.get(
          `${process.env.VUE_APP_API_BASE_URL}products`,
          {
            headers: {
              Authorization: `Bearer ${token}`,
            },
          }
        );
        this.products = response.data.data;
        console.log(response);

        this.$nextTick(() => {
          $("#productsTable").DataTable();
        });
      } catch (error) {
        console.error("Error fetching products:", error);
      }
    },
  },
};
</script>

<style scoped>
/* Add any custom styling here */
</style>
