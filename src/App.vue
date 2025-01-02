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
      <div class="user-info" v-if="user">
        {{ user.username }}
      </div>
    </nav>

    <hr />

    <!-- Page Components -->
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
    <BookingsPage 
      v-show="activePage === 'bookings'" 
      :user="user" 
      v-if="user"
    />
    <OwnerCampingsPage v-show="activePage === 'owned campings'" :user="user" />
  </div>
</template>

<script>
import HomePage from "./components/HomePage.vue";
import LoginPage from "./components/LoginPage.vue";
import RegisterPage from "./components/RegisterPage.vue";
import CampingsPage from "./components/CampingsPage.vue";
import AddCampingPage from "./components/AddCampingPage.vue";
import BookingsPage from "./components/BookingsPage.vue";
import OwnerCampingsPage from "./components/OwnerCampingsPage.vue";

export default {
  name: "App",
  components: {
    HomePage,
    LoginPage,
    RegisterPage,
    CampingsPage,
    AddCampingPage,
    BookingsPage,
    OwnerCampingsPage,
  },
  data() {
    return {
      activePage: "home", // Default to login page
      pages: ["home", "login"], // Base pages always visible
      user: null, // Store logged-in user details
    };
  },
  watch: {
    // Watch for changes in the user state
    user(newValue) {
      // Add pages for logged-in users
      if (!this.pages.includes("campings")) {
          this.pages.push("campings");
        }
        if (!this.pages.includes("bookings")) {
          this.pages.push("bookings");
        }
        if (newValue.role === "owner" && !this.pages.includes("owned campings")) {
          this.pages.push("owned campings");
        }
      else {
        // Remove pages when logged out
        this.pages = this.pages.filter(
          (page) => !["campings", "bookings", "owned campings"].includes(page)
        );
      }
    },
  },
  methods: {
    navigate(page) {
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
      this.user = userDetails; // Store user details upon login
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
  padding-top: 60px;
}

nav {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  background-color: white;
  z-index: 10;
  border-bottom: 2px solid #ccc;
}

.nav-list {
  list-style-type: none;
  padding: 10px 0;
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
  margin-right: 20px;
  margin-top: 18px;
  top: 10px;
  right: 10px;
  background-color: #f0f0f0;
  padding: 5px 10px;
  border-radius: 5px;
  font-size: 20px;
}
</style>