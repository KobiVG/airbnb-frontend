<template>
  <div class="campings-page">
    <h1>My Campings</h1>
    <div v-if="campingSpots.length === 0" class="no-campings">
      <p>You do not own any campings.</p>
    </div>
    <div class="camping-list">
      <div
        class="camping-item"
        v-for="camping in campingSpots"
        :key="camping.camping_spot_id" 
      >
        <img :src="`http://localhost:3000${camping.image}`" alt="Camping image" class="camping-image" />
        <div class="camping-details">
          <h2>{{ camping.name }}</h2>
          <p>{{ camping.description }}</p>
          <p><strong>Location:</strong> {{ camping.location }}</p>
          <p><strong>Price per night:</strong> €{{ camping.price_per_night }}</p>
        </div>
        <div class="camping-actions">
          <button @click="deleteCampingSpot(camping.camping_spot_id)" class="camping-button">Delete</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "OwnerCampingsPage",
  props: {
    user: Object,
  },
  data() {
    return {
      campingSpots: [],
      tallestItemHeight: 0,
    };
  },
  methods: {
    async fetchOwnerCampings() {
      try {
        const response = await axios.get(
          `http://localhost:3000/api/owner-camping-spots/${this.user.userId}`
        );
        this.campingSpots = response.data;
        this.setTallestItemHeight();
      } catch (error) {
        console.error("Error fetching owner's camping spots:", error);
      }
    },
    setTallestItemHeight() {
      this.$nextTick(() => {
        const campingItems = document.querySelectorAll(".camping-item");
        let maxHeight = 0;
        campingItems.forEach(item => {
          maxHeight = Math.max(maxHeight, item.offsetHeight);
        });
        this.tallestItemHeight = maxHeight;
      });
    },
    async deleteCampingSpot(campingId) {
      try {
        console.log("Deleting camping spot with ID:", campingId);
        const response = await axios.delete(
          `http://localhost:3000/api/camping-spot/${campingId}`
        );
        console.log("Camping spot deleted successfully:", response.data);

        this.$emit("refresh-deletedCampings");
        this.$emit("refresh-deletedBookings");

        this.campingSpots = this.campingSpots.filter(
          (camping) => camping.camping_spot_id !== campingId
        );
        this.setTallestItemHeight();
      } catch (error) {
        console.error("Error deleting camping spot:", error);
      }
    },
  },
  watch: {
    user: {
      handler(newUser) {
        if (newUser && newUser.userId) {
          this.campingSpots = [];
          this.fetchOwnerCampings();
        } else {
          this.campingSpots = [];
        }
      },
      immediate: true,
    },
  },
  mounted() {
    if (this.user?.userId) {
      this.fetchOwnerCampings();
    }
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
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  border: 1px solid #ccc;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  background-color: white;
  padding: 10px;
  display: flex;
  flex-direction: column;
  min-height: 400px;
}

.camping-details {
  flex-grow: 1;
}

.camping-button {
  background-color: #f6ab59;
  color: white;
  border: none;
  padding: 10px 20px;
  font-size: 1em;
  cursor: pointer;
  border-radius: 5px;
  text-align: center;
  width: 100%;
  margin-top: 10px;
}

.camping-button:hover {
  background-color: #f28f1f;
}

.camping-image {
  width: 100%;
  height: 200px;
  object-fit: cover;
  border-radius: 5px;
}

.camping-item h2 {
  margin: 10px 0;
  font-size: 1.5rem;
  text-align: center;
}

.camping-item p {
  margin: 5px 10px;
  font-size: 1rem;
}

.camping-item p strong {
  font-weight: bold;
}

.camping-actions {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}

.no-campings {
  text-align: center;
  margin-top: 20px;
  font-size: 1.2em;
  color: #888;
}
</style>
