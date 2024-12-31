<template>
  <div id="app">
    <nav>
      <ul class="nav-list">
        <li v-for="(page, index) in pages" :key="index" @click="navigate(page)" :class="{ active: activePage === page }" >
          {{ page }}
        </li>
      </ul>
      <!-- Display username in the top-right corner -->
      <div class="user-info" v-if="user">
        {{ user.username }}
      </div>
    </nav>

    <hr />

    <HomePage v-show="activePage === 'home'" />
    <LoginPage
      v-show="activePage === 'login'"
      ref="loginPage"
      @navigate-register="showRegisterPage"
      @login-success="handleLoginSuccess"
      @user-data="setUser"
    />
    <RegisterPage
      v-show="activePage === 'register'"
      @navigate-login="showLoginPage"
      @register-success="handleRegisterSucces"
    />
  </div>
</template>

<script>
import HomePage from "./components/HomePage.vue";
import LoginPage from "./components/LoginPage.vue";
import RegisterPage from "./components/RegisterPage.vue";

export default {
  name: "App",
  components: {
    HomePage,
    LoginPage,
    RegisterPage,
  },
  data() {
    return {
      activePage: "login", // Start with the login page
      pages: ["home", "login"], // Register page is not part of the nav list
      user: null, // Store user details
    };
  },
  methods: {
    navigate(page) {
      this.activePage = page;

      if (page === "login") {
        this.$refs.loginPage?.resetState();
      }
    },
    handleLoginSuccess() {
      this.navigate("home");
    },
    handleRegisterSucces() {
      this.navigate("login");
    },
    showRegisterPage() {
      this.navigate("register");
    },
    showLoginPage() {
      this.navigate("login");
    },
    setUser(userDetails) {
      this.user = userDetails; // Store user data
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.nav-list {
  list-style-type: none;
  padding: 0;
  display: flex;
  justify-content: center;
  gap: 20px;
}

.nav-list li {
  cursor: pointer;
  padding: 10px 20px;
  border-radius: 5px;
  transition: background-color 0.3s;
}

.nav-list li:hover {
  background-color: #f0f0f0;
}

.nav-list li.active {
  background-color: #2c3e50;
  color: white;
}

.user-info {
  position: absolute;
  top: 10px;
  right: 10px;
  background-color: #f0f0f0;
  padding: 5px 10px;
  border-radius: 5px;
  font-size: 14px;
}
</style>
