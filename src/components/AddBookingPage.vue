<template>
    <div class="bookings-page">
      <h1>Book Camping Spot</h1>
  
      <div v-if="campingSpotDetails">
        <p><strong>{{ campingSpotDetails.name }}</strong></p>
        <p>{{ campingSpotDetails.description }}</p>
        <p><strong>Price per night:</strong> €{{ campingSpotDetails.price_per_night }}</p>
  
        <form @submit.prevent="submitBooking" class="booking-form">
          <label for="checkInDate">Check-in Date:</label>
          <input v-model="checkInDate" type="date" id="checkInDate" required />
  
          <label for="checkOutDate">Check-out Date:</label>
          <input v-model="checkOutDate" type="date" id="checkOutDate" required />
  
          <button type="submit" class="camping-button">Confirm Booking</button>
        </form>
      </div>
  
      <div v-else>
        <p>Loading camping spot details...</p>
      </div>
    </div>
  </template>
  
  <script>
  import axios from "axios";
  
  export default {
    name: "AddBookingPage",
    props: {
      user: {
        type: Object,
        required: true,
      },
      campingSpotDetails: {
        type: Object,
        required: true,
        default: () => ({})
      },
    },
    data() {
      return {
        checkInDate: "",
        checkOutDate: "",
      };
    },
    methods: {
      async submitBooking() {
        try {
          if (!this.user?.userId) {
            throw new Error("User is not logged in or userId is missing.");
          }

          if (!this.campingSpotDetails?.campingSpotId) {
            throw new Error("Camping spot details are incomplete.");
          }

          await axios.post("http://localhost:3000/api/book-camping", {
            userId: this.user.userId,
            campingSpotId: this.campingSpotDetails.campingSpotId,
            checkInDate: this.checkInDate,
            checkOutDate: this.checkOutDate,
          });

          this.$emit("booking-success");
          this.$emit("refresh-bookings");
        } catch (error) {
          console.error("Error booking camping spot:", error);
          alert("There was an error while booking. Please try again.");
        }
      },
    },
  };
  </script>
  
  <style scoped>
  .bookings-page {
    padding: 20px;
  }
  
  h1 {
    text-align: center;
    font-size: 2rem;
    margin-bottom: 20px;
  }
  
  .booking-form {
    display: flex;
    flex-direction: column;
    gap: 15px;
    max-width: 400px;
    margin: 0 auto;
  }
  
  .booking-form label {
    font-size: 1rem;
    margin-bottom: 5px;
  }
  
  .booking-form input {
    padding: 10px;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 5px;
    margin-bottom: 15px;
  }
  
  .camping-button {
    background-color: #f6ab59;
    color: white;
    border: none;
    padding: 10px 20px;
    font-size: 1.1rem;
    cursor: pointer;
    border-radius: 5px;
    transition: background-color 0.3s ease;
  }
  
  .camping-button:hover {
    background-color: #e5974e;
  }
  
  .camping-button:focus {
    outline: none;
  }
  </style>
  