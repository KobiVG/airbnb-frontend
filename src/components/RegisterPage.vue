<template>
  <div class="register-page">
    <h1>Register</h1>
    <form @submit.prevent="register" class="register-form">
      <div class="form-group">
        <label>Username:</label>
        <input
          type="text" v-model="username" required class="form-control" />
      </div>

      <div class="form-group">
        <label>Email:</label>
        <input type="email" v-model="email" required class="form-control" />
      </div>

      <div class="form-group">
        <label>Password:</label>
        <input type="password" v-model="password" required class="form-control" />
      </div>

      <div class="form-group">
        <label>Role:</label>
        <select v-model="role" class="form-control">
          <option>User</option>
          <option>Owner</option>
        </select>
      </div>

      <div class="form-group">
        <button type="submit" class="btn btn-primary">Register</button>
      </div>
    </form>

    <p>Already have an account? <button @click="goToLogin">Login here</button></p>

    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
    <p v-if="successMessage" class="success">{{ successMessage }}</p>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      username: "",
      email: "",
      password: "",
      role: "User",
      errorMessage: "",
      successMessage: "",
    };
  },
  methods: {
    async register() {
      try {
        const response = await axios.post("http://localhost:3000/api/register", {
          username: this.username,
          email: this.email,
          password: this.password,
          role: this.role,
        });
        this.successMessage = response.data.message;
        this.$emit("register-success");
        this.errorMessage = "";
      } catch (error) {
        // Log the full error for debugging purposes
        console.error("Registration error:", error.response);
        
        this.errorMessage =
          error.response?.data?.message || "An error occurred.";
        this.successMessage = "";
      }
    },
    goToLogin() {
      this.$emit('navigate-login'); // Emit event to navigate to the login page
    }
  }
};
</script>

<style scoped>
.register-page {
  max-width: 400px;
  margin: 0 auto;
  padding: 25px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.register-form .form-group {
  margin-bottom: 15px;
}

.register-form .form-control {
  width: 94.5%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.register-form .btn {
  width: 100%;
  padding: 10px;
  background-color: #007bff;
  border: none;
  border-radius: 5px;
  color: white;
  font-size: 16px;
}

.register-page p {
  margin-top: 15px;
}

.error {
  color: red;
}

.success {
  color: green;
}
</style>
