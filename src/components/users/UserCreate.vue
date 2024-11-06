<template>
  <div class="container mt-5">
    <div class="card">
      <div class="card-header">
        <h5>Create New Product</h5>
      </div>
      <div class="card-body">
        <form @submit.prevent="submitForm">
          <div class="row">
            <div class="col-6 mb-3">
              <label for="product_name" class="form-label">Product Name</label>
              <input
                type="text"
                class="form-control"
                id="product_name"
                placeholder="Enter product name"
                v-model="product.product_name"
              />
              <small v-if="errors.product_name" class="text-danger">{{ errors.product_name }}</small>
            </div>
            <div class="col-6 mb-3">
              <label for="price" class="form-label">Price</label>
              <input
                type="number"
                class="form-control"
                id="price"
                placeholder="Enter price"
                v-model="product.price"
              />
              <small v-if="errors.price" class="text-danger">{{ errors.price }}</small>
            </div>
          </div>
          <div class="row">
            <div class="col-6 mb-3">
              <label for="stock" class="form-label">Stock</label>
              <input
                type="number"
                class="form-control"
                id="stock"
                placeholder="Enter stock quantity"
                v-model="product.stock"
              />
              <small v-if="errors.stock" class="text-danger">{{ errors.stock }}</small>
            </div>
            <div class="col-6 mb-3">
              <label for="image" class="form-label">Image</label>
              <input
                type="file"
                class="form-control"
                id="image"
                @change="handleImageUpload"
              />
              <small v-if="errors.image" class="text-danger">{{ errors.image }}</small>
            </div>
          </div>
          <div class="mb-3">
            <label for="description" class="form-label">Description</label>
            <textarea
              class="form-control"
              id="description"
              rows="3"
              placeholder="Enter product description"
              v-model="product.description"
            ></textarea>
            <small v-if="errors.description" class="text-danger">{{ errors.description }}</small>
          </div>
          <button type="submit" class="btn btn-primary">Create Product</button>
          <router-link to="/products" class="btn btn-secondary ms-2">Cancel</router-link>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import * as Yup from "yup";

export default {
  name: "ProductCreate",
  data() {
    return {
      product: {
        product_name: "",
        price: 0,
        stock: 0,
        description: "",
      },
      imageFile: null,
      errors: {},
    };
  },
  methods: {
    handleImageUpload(event) {
      this.imageFile = event.target.files[0]; // Store selected file in data
    },
    async validateForm() {
      const schema = Yup.object().shape({
        product_name: Yup.string().required("Product name is required"),
        price: Yup.number().positive("Price must be a positive number").required("Price is required"),
        stock: Yup.number().integer("Stock must be an integer").required("Stock is required"),
        description: Yup.string().required("Description is required"),
      });

      try {
        await schema.validate(this.product, { abortEarly: false });
        this.errors = {}; // Clear errors if validation passes
        return true;
      } catch (validationErrors) {
        this.errors = {};
        validationErrors.inner.forEach((error) => {
          this.errors[error.path] = error.message;
        });
        return false;
      }
    },
    async submitForm() {
      const isValid = await this.validateForm();
      if (!isValid || !this.imageFile) {
        if (!this.imageFile) this.errors.image = "Image is required";
        return;
      }

      const formData = new FormData();
      formData.append("product_name", this.product.product_name);
      formData.append("price", this.product.price);
      formData.append("stock", this.product.stock);
      formData.append("description", this.product.description);
      formData.append("image", this.imageFile); // Add image file to FormData

      try {
        const token = localStorage.getItem("token");
        const response = await axios.post(
          `${process.env.VUE_APP_API_BASE_URL}products-create`,
          formData,
          {
            headers: {
              Authorization: `Bearer ${token}`,
              "Content-Type": "multipart/form-data",
            },
          }
        );
        console.log("Product created:", response.data);
        this.$router.push("/users");
      } catch (error) {
        console.error("Error creating product:", error);
      }
    },
  },
};
</script>

<style scoped>
/* Add any custom styling here */
</style>
