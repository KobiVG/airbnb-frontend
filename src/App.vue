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
        <p @click="navigateToChangeUserInfo">{{ user.username }}</p>
      </div>
    </nav>

    <hr />

    <!-- Page Components -->
    <HomePage 
      v-show="activePage === 'home'" 
    />
    <LoginPage 
      ref="loginPage"
      v-show="activePage === 'login'" 
      @navigate-register="showRegisterPage" 
      @login-success="handleLoginSuccess,refreshBookings,refreshCampings,refreshOwnedCampings" 
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
      @navigate-addbooking="showAddBookingPage"
      ref="campingsPage" 
    />
    <AddCampingPage 
      v-show="activePage === 'add-camping'" 
      :user="user" 
      @addingCamping-success="handleAddCampingSucces"
      @refresh-campings="refreshCampings"
      @refresh-ownedCampings="refreshOwnedCampings"
    />
    <BookingsPage 
      v-show="activePage === 'bookings'" 
      :user="user" 
      v-if="user"
      ref="bookingsPage"
    />
    <OwnerCampingsPage 
      v-show="activePage === 'owned campings'" 
      :user="user" 
      v-if="user"
      ref="ownerCampingsPage"
      @refresh-deletedCampings="refreshCampings"
      @refresh-deletedBookings="refreshBookings"
    />
    <ChangeUserInformationPage 
      v-show="activePage === 'change-user-info'" 
      :user="user" 
      v-if="user" 
      @navigate-back="navigate('home')" 
      @user-updated="updateUser" 
    />
    <AddBookingPage 
      v-show="activePage === 'add-booking'" 
      :user="user" 
      v-if="user" 
      @booking-success="handleBookingSuccess" 
      :campingSpotDetails="campingSpotDetails || {}"
      @refresh-bookings="refreshBookings"
    />
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
import ChangeUserInformationPage from "./components/ChangeUserInformationPage.vue";
import AddBookingPage from "./components/AddBookingPage.vue";

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
    ChangeUserInformationPage,
    AddBookingPage,
  },
  data() {
    return {
      activePage: "home", // Default to login page
      pages: ["home", "login"], // Base pages always visible
      user: null, // Store logged-in user details
      campingSpotDetails: null, // Store the full camping spot details passed from CampingsPage
    };
  },
  methods: {
    navigate(page) {
      this.activePage = page;
      
      // Reset login state when navigating back to login
      if (page === "login") {
        this.$refs.loginPage?.resetState();
      }
      if (page === "add-camping") {
        this.$refs.AddCampingPage?.resetState();
      }
    },
    handleLoginSuccess() {
      this.navigate("home");
    },
    handleRegisterSuccess() {
      this.navigate("login");
    },
    handleAddCampingSucces() {
      this.navigate("campings");
    },
    handleBookingSuccess() {
      this.navigate("bookings");
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
    showAddBookingPage(campingSpotDetails) {
      this.campingSpotDetails = campingSpotDetails;
      this.navigate("add-booking");
    },
    navigateToChangeUserInfo() {
      if (this.user) {
        this.activePage = "change-user-info";
      }
    },
    refreshBookings() {
      this.$refs.bookingsPage.fetchBookings();
      this.$refs.ownerCampingsPage.fetchOwnerCampings();
    },
    refreshCampings() {
      this.$refs.campingsPage.fetchCampingSpots();
      this.$refs.ownerCampingsPage.fetchOwnerCampings();
    },
    refreshOwnedCampings() {
      this.$refs.OwnerCampingsPage.fetchOwnerCampings();
    },
    updateUser(updatedUser) {
      this.user = updatedUser;
    },
    setUser(userDetails) {
      // Clear state related to the previous user
      this.user = null;
      this.pages = ["home", "login"]; // Reset pages to the default

      // Set new user
      this.user = userDetails;

      // Add role-based pages
      if (userDetails.role === "owner") {
        this.pages.push("owned campings");
      }
      if (!this.pages.includes("campings")) {
        this.pages.push("campings");
      }
      if (!this.pages.includes("bookings")) {
        this.pages.push("bookings");
      }
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
  margin-top: -1px;
  top: 10px;
  right: 10px;
  background-color: #f0f0f0;
  padding: 5px 10px;
  border-radius: 5px;
  font-size: 20px;
}
</style>