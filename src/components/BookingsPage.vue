<template>
    <div class="user-bookings">
      <h1>My bookings</h1>
      <div v-if="bookings.length === 0" class="no-bookings">
        <p>You haven't made any bookings.</p>
      </div>
      <div v-else class="bookings-list">
        <div v-for="booking in bookings" :key="booking.booking_id" class="booking-item">
          <h3>{{ booking.camping_name }}</h3>
          <p><strong>Location:</strong> {{ booking.camping_location }}</p>
          <p><strong>Check-in:</strong> {{ booking.check_in_date }}</p>
          <p><strong>Check-out:</strong> {{ booking.check_out_date }}</p>
          <p><strong>Status:</strong> {{ booking.status }}</p>
          <p><strong>Price/night:</strong> €{{ booking.price_per_night }}</p>
        </div>
      </div>
    </div>
  </template>
  
<script>
  import axios from "axios";
  
  export default {
  name: "UserBookings",
  props: {
    user: Object,
  },
    data() {
    return {
        bookings: [],
        userId: this.user ? this.user.userId : null,
    };
    },
    methods: {
    async fetchBookings() {
        try {
        const response = await axios.get(
            `http://localhost:3000/api/user-bookings/${this.userId}`
        );
        this.bookings = response.data;
        } catch (error) {
        console.error("Nog geen boekingen gemaakt.", error);
        }
    },
    },
    created() {
    if (this.userId) {
        this.fetchBookings();
    }
    },
};
</script>
  
  <style scoped>
  .user-bookings {
    padding: 20px;
  }
  
  .bookings-list {
    display: flex;
    flex-direction: column;
    gap: 15px;
  }
  
  .booking-item {
    border: 1px solid #ddd;
    border-radius: 5px;
    padding: 15px;
    background-color: #f9f9f9;
  }
  
  h3 {
    margin: 0 0 10px;
  }
  
  .no-bookings {
    text-align: center;
    margin-top: 20px;
    font-size: 1.2em;
    color: #888;
  }
  </style>  