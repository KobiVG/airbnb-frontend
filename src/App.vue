<template>
  <div id="app">
    <nav>
      <ul class="nav-list">
        <li
          v-for="(page, index) in pages"
          :key="index"
          @click="navigate(page)"
          :class="{ active: activePage === page }"
        >
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
      @register-success="handleRegisterSuccess"
    />
    <CampingsPage 
      v-show="activePage === 'campings'" 
      :user="user" 
      @navigate-add-camping="showAddCampingPage"
    />
    <AddCampingPage v-show="activePage === 'add-camping'" />
  </div>
</template>

<script>
import HomePage from "./components/HomePage.vue";
import LoginPage from "./components/LoginPage.vue";
import RegisterPage from "./components/RegisterPage.vue";
import CampingsPage from "./components/CampingsPage.vue";
import AddCampingPage from "./components/AddCampingPage.vue";

export default {
  name: "App",
  components: {
    HomePage,
    LoginPage,
    RegisterPage,
    CampingsPage,
    AddCampingPage,
  },
  data() {
    return {
      activePage: "login", // Default to login page
      pages: ["home", "login"], // Base pages
      user: null, // Store logged-in user details
    };
  },
  watch: {
    // Watch for changes in the user state
    user(newValue) {
      if (newValue) {
        // Add "campings" to the navigation when logged in
        if (!this.pages.includes("campings")) {
          this.pages.push("campings");
        }
      } else {
        // Remove "campings" from the navigation when logged out
        this.pages = this.pages.filter((page) => page !== "campings");
      }
    },
  },
  methods: {
    navigate(page) {
      // Prevent unauthorized access to the campings page
      if (page === "campings" && !this.user) {
        alert("You must be logged in to access this page.");
        this.activePage = "login";
        return;
      }

      this.activePage = page;

      // Reset login state when navigating back to login
      if (page === "login") {
        this.$refs.loginPage?.resetState();
      }
    },
    handleLoginSuccess() {
      this.navigate("home");
    },
    handleRegisterSuccess() {
      this.navigate("login");
    },
    showRegisterPage() {
      this.navigate("register");
    },
    showLoginPage() {
      this.navigate("login");
    },
    showAddCampingPage() {
      this.navigate("add-camping");
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
  margin-top: 20px;
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