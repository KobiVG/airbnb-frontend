<template>
  <div class="login-page">
    <h1>Login</h1>
    <form @submit.prevent="login" class="login-form">
      <div class="form-group">
        <label>Email:</label>
        <input
          type="email"
          v-model="email"
          required
          class="form-control"
        />
      </div>

      <div class="form-group">
        <label>Wachtwoord:</label>
        <input
          type="password"
          v-model="password"
          required
          class="form-control"
        />
      </div>

      <div class="form-group">
        <button type="submit" class="btn btn-primary">Inloggen</button>
      </div>
    </form>
    <p v-if="error" class="error-message">{{ error }}</p>

    <!-- Link to register page -->
    <div class="register-link">
      <p>Heb je geen account? <button @click="goToRegister">Registreer hier</button></p>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      email: "",
      password: "",
      error: null,
    };
  },
  methods: {
    resetState() {
      console.log("Resetting login state.");
      this.email = "";
      this.password = "";
      this.error = null;
    },
    async login() {
      console.log("Login button clicked.");
      try {
        const loginResponse = await axios.post("http://localhost:3000/api/login", {
          email: this.email,
          password: this.password,
        });

        console.log("Login successful:", loginResponse.data);

        // Fetch user details after login
        const userDetailsResponse = await axios.get(
          `http://localhost:3000/api/users?email=${this.email}`
        );

        const userDetails = userDetailsResponse.data;
        console.log("Fetched user details:", userDetails);

        // Pass user details to parent component
        this.$emit("user-data", userDetails);

        this.$emit("login-success"); // Notify login success
        this.resetState();
      } catch (err) {
        this.error = err.response?.data?.error || "Er ging iets mis.";
        console.error("Login failed:", this.error);
      }
    },
    goToRegister() {
      this.$emit('navigate-register'); // Emit event to navigate to the register page
    }
  },
};
</script>

<style scoped>
.login-page {
  max-width: 400px;
  margin: 0 auto;
  padding: 25px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.login-form .form-group {
  margin-bottom: 15px;
}

.login-form .form-control {
  width: 94.5%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.login-form .btn {
  width: 100%;
  padding: 10px;
  background-color: #007bff;
  border: none;
  border-radius: 5px;
  color: white;
  font-size: 16px;
}

.error-message {
  color: red;
  margin-top: 10px;
}

.register-link {
  margin-top: 15px;
}
</style>
