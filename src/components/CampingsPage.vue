<template>
  <div class="campings-page">
    <h1>Available Camping Spots</h1>
    
    <div class="filter">
      <label for="minPrice">Min Price:</label>
      <input v-model="filters.minPrice" type="number" id="minPrice" placeholder="Min price" />

      <label for="maxPrice">Max Price:</label>
      <input v-model="filters.maxPrice" type="number" id="maxPrice" placeholder="Max price" />

      <label for="location">Location:</label>
      <input v-model="filters.location" type="text" id="location" placeholder="Enter location" />

      <button @click="applyFilters" class="camping-button">Apply Filters</button>
    </div>

    <button v-if="user && user.role === 'owner'" @click="goToAddCampingPage" class="camping-button">Add New Camping Spot</button>
    
    <div class="camping-list">
      <div class="camping-item" v-for="camping in filteredCampingSpots" :key="camping.id">
        <img :src="`http://localhost:3000${camping.image}`" alt="Camping image" class="camping-image" />
        <h2>{{ camping.name }}</h2>
        <p>{{ camping.description }}</p>
        <p><strong>Location:</strong> {{ address[camping.location] || 'Loading...' }}</p>
        <p><strong>Price per night:</strong> €{{ camping.price_per_night }}</p>

        <button class="book-camping-button" @click="goToAddBookingPage(camping.id)">Book this camping</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "CampingsPage",
  data() {
    return {
      campingSpots: [],
      filteredCampingSpots: [],
      address: {},
      filters: {
        minPrice: null,
        maxPrice: null,
        location: "",
      },
    };
  },
  methods: {
    async fetchCampingSpots() {
      try {
        const response = await axios.get("http://localhost:3000/api/camping-spots");
        this.campingSpots = response.data;
        this.filteredCampingSpots = this.campingSpots;
        this.fetchAddresses();
      } catch (error) {
        console.error("Error fetching camping spots:", error);
      }
    },
    
    async fetchAddresses() {
      for (const camping of this.campingSpots) {
        await this.fetchAddress(camping.location);
      }
    },

    async fetchAddress(coordinates) {
      try {
        const [lat, lon] = coordinates.split(",");
        const response = await axios.get(
          `https://nominatim.openstreetmap.org/reverse?lat=${lat}&lon=${lon}&format=json`
        );
        this.$set(this.address, coordinates, response.data.display_name);
      } catch (error) {
        console.error("Error fetching address:", error);
      }
    },

    applyFilters() {
      this.filteredCampingSpots = this.campingSpots.filter((camping) => {
        const priceCondition =
          (this.filters.minPrice ? camping.price_per_night >= this.filters.minPrice : true) &&
          (this.filters.maxPrice ? camping.price_per_night <= this.filters.maxPrice : true);

        const locationCondition =
          this.filters.location
            ? (this.address[camping.location] || '').toLowerCase().includes(this.filters.location.toLowerCase())
            : true;

        return priceCondition && locationCondition;
      });
    },

    async goToAddBookingPage(campingSpotId) {
      const campingSpot = this.campingSpots.find(spot => spot.id === campingSpotId);

      const campingSpotDetails = {
        campingSpotId: campingSpot.id,
        name: campingSpot.name,
        location: campingSpot.location,
        description: campingSpot.description,
        price_per_night: campingSpot.price_per_night,
      };

      this.$emit("navigate-addbooking", campingSpotDetails);
    },

    goToAddCampingPage() {
      this.$emit("navigate-add-camping");
    },
  },
  mounted() {
    this.fetchCampingSpots();
  },
  props: {
    user: Object,
  },
};
</script>

<style scoped>
  .campings-page {
    padding: 20px;
  }

  .filter {
    margin-bottom: 9px;
  }

  .filter label {
    margin-right: 10px;
  }

  .filter input {
    margin-right: 10px;
  }

  .camping-list {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    justify-content: center;
  }

  .camping-item {
    width: 300px;
    min-height: 450px;
    border: 1px solid #ccc;
    border-radius: 10px;
    overflow: hidden;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }

  .camping-button {
    background-color: #f6ab59;
    color: white;
    border: none;
    padding: 10px 20px;
    margin-bottom: 20px;
    margin-top: -20px;
    font-size: 1em;
    cursor: pointer;
    border-radius: 5px;
  }

  .book-camping-button {
    background-color: #f6ab59;
    color: white;
    border: none;
    padding: 10px 20px;
    font-size: 1em;
    cursor: pointer;
    border-bottom-right-radius: 5px;
    text-align: center;
    width: 100%;
    margin-top: auto;
  }

  .camping-image {
    width: 100%;
    height: 200px;
    object-fit: cover;
  }

  .camping-item h2 {
    margin: 10px 0;
    font-size: 1.5rem;
  }

  .camping-item p {
    margin: 5px 10px;
  }
</style>