<template>
  <div class="add-camping-page">
    <h1>Add New Camping Spot</h1>
    <form @submit.prevent="submitCampingSpot">
      <div>
        <label for="name">Camping Spot Name</label>
        <input type="text" id="name" v-model="newCamping.name" required />
      </div>
      <div>
        <label for="description">Description</label>
        <textarea id="description" v-model="newCamping.description" required></textarea>
      </div>
      <div>
        <label for="location-2">Location (latitude, longitude)</label>
        <input type="text" id="location-2" v-model="newCamping.location" required />
      </div>
      <div>
        <label for="price_per_night">Price per night (€)</label>
        <input type="number" id="price_per_night" v-model="newCamping.price_per_night" required />
      </div>
      <div>
        <label for="image">Upload Image</label>
        <input type="file" id="image" @change="handleImageUpload" required />
      </div>
      <button type="submit">Submit</button>
    </form>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  props: ['user'],
  data() {
    return {
      newCamping: {
        name: '',
        description: '',
        location: '',
        price_per_night: null,
        image: null,
      },
    };
  },
  methods: {
    resetState() {
      this.newCamping.name = "";
      this.newCamping.description = "";
      this.newCamping.location = "";
      this.newCamping.price_per_night = null;
      this.newCamping.image = null;
    },
    async submitCampingSpot() {
      const formData = new FormData();
      formData.append('owner_user_id', this.user.userId);
      formData.append('name', this.newCamping.name);
      formData.append('description', this.newCamping.description);
      formData.append('location', this.newCamping.location);
      formData.append('price_per_night', this.newCamping.price_per_night);

      if (this.newCamping.image) {
        formData.append('image', this.newCamping.image);
      }

      this.resetState();

      try {
        await axios.post('http://localhost:3000/api/camping-spots', formData, {
          headers: {
            'Content-Type': 'multipart/form-data',
          },
        });

        this.$emit("addingCamping-success");
        this.$emit("refresh-campings");
        this.$emit("refresh-ownedCampings");
      } catch (error) {
        alert("Failed to add new camping spot.");
      }
    },

    handleImageUpload(event) {
      this.newCamping.image = event.target.files[0];
    },
  },
};
</script>

  
  <style scoped>
  .add-camping-page {
    padding: 20px;
    max-width: 600px;
    margin: 0 auto;
  }
  
  form div {
    margin-bottom: 10px;
  }
  
  label {
    display: block;
    margin-bottom: 5px;
  }
  
  input, textarea {
    width: 100%;
    padding: 8px;
    font-size: 14px;
    border: 1px solid #ccc;
    border-radius: 4px;
  }

  textarea {
    resize: none;
    height: 100px;
  }
  
  button {
    padding: 10px 20px;
    background-color: #f6ab59;
    color: white;
    border: none;
    cursor: pointer;
    border-radius: 5px;
  }
  
  button:hover {
    background-color: #45a049;
  }
  </style>
  