<template>
    <div class="campings-page">
      <h1>Available Camping Spots</h1>
      <button v-if="user && user.role === 'owner'" @click="goToAddCampingPage" class="camping-button">Add New Camping Spot</button>
      
      <div class="camping-list">
        <div class="camping-item" v-for="camping in campingSpots" :key="camping.name">
          <img :src="`${camping.image}`" alt="Camping image" class="camping-image" />
          <h2>{{ camping.name }}</h2>
          <p>{{ camping.description }}</p>
          <p><strong>Location:</strong> {{ address[camping.location] || 'Loading...' }}</p>
          <p><strong>Price per night:</strong> €{{ camping.price_per_night }}</p>
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
        address: {},
      };
    },
    methods: {
      async fetchCampingSpots() {
        try {
          const response = await axios.get("http://localhost:3000/api/camping-spots");
          this.campingSpots = response.data;
  
          // Fetch addresses for each camping spot
          this.campingSpots.forEach((camping) => {
            this.fetchAddress(camping.location);
          });
        } catch (error) {
          console.error("Error fetching camping spots:", error);
        }
    },
      async fetchAddress(coordinates) {
        try {
          const response = await axios.get(
            `https://nominatim.openstreetmap.org/reverse?lat=${coordinates.split(",")[0]}&lon=${
              coordinates.split(",")[1]
            }&format=json`
          );
          this.$set(this.address, coordinates, response.data.display_name);
        } catch (error) {
          console.error("Error fetching address:", error);
        }
    },
      goToAddCampingPage() {
        this.$emit('navigate-add-camping');
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
  
  .camping-list {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    justify-content: center;
  }
  
  .camping-item {
    width: 300px;
    border: 1px solid #ccc;
    border-radius: 10px;
    overflow: hidden;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  }

  .camping-button {
    background-color: #f6ab59;
    color: white;
    border: none;
    padding: 10px 20px;
    margin-bottom: 20px;
    font-size: 1em;
    cursor: pointer;
    border-radius: 5px;
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
  