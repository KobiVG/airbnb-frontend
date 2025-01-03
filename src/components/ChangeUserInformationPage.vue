<template>
    <div class="change-user-info-page" v-if="user">
      <h1>Change User Information</h1>
      <form @submit.prevent="updateUserInfo" class="user-info-form">
        <div class="form-group">
          <label>Username:</label>
          <input
            type="text"
            v-model="username"
            class="form-control"
          />
        </div>
  
        <div class="form-group">
          <label>Email:</label>
          <input
            type="email"
            v-model="email"
            class="form-control"
          />
        </div>
  
        <div class="form-group">
          <label>Role:</label>
          <select v-model="role" class="form-control">
            <option>User</option>
            <option>Owner</option>
          </select>
        </div>
  
        <div class="form-group">
          <button type="submit" class="btn btn-primary">Update Information</button>
        </div>
      </form>
    </div>
    <div v-else>
      <p>Loading user information...</p>
    </div>
  </template>  
  
  <script>
  import axios from "axios";
  
  export default {
    props: {
      user: {
        type: Object,
        default: () => ({
          username: '',
          email: '',
          role: 'User',
          userId: null,
        }),
      },
    },
    data() {
      return {
        username: this.user.username || '',
        email: this.user.email || '',
        role: this.user.role || 'User',
      };
    },
    watch: {
      user: {
        immediate: true,
        handler(newVal) {
          this.username = newVal.username || '';
          this.email = newVal.email || '';
          this.role = newVal.role || 'User';
        },
      },
    },
    methods: {
        async updateUserInfo() {
            try {
                const updatedFields = {};
                if (this.username !== this.user.username) updatedFields.username = this.username;
                if (this.email !== this.user.email) updatedFields.email = this.email;
                if (this.role !== this.user.role) updatedFields.role = this.role;

                if (Object.keys(updatedFields).length === 0) {
                return; // No changes to update
                }

                await axios.put(
                `http://localhost:3000/api/users/${this.user.userId}`,
                updatedFields
                );

                // Emit updated user details to parent
                this.$emit("user-updated", { ...this.user, ...updatedFields });
            } catch (error) {
                console.error(error.response?.data?.error || "An error occurred.");
            }
        },
    },
  };
  </script>
  
  <style scoped>
  .change-user-info-page {
    max-width: 400px;
    margin: 0 auto;
    padding: 25px;
    border: 1px solid #ccc;
    border-radius: 5px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    margin-top: 50px;
  }
  
  .user-info-form .form-group {
    margin-bottom: 15px;
  }
  
  .user-info-form .form-control {
    width: 94.5%;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
  }
  
  .user-info-form .btn {
    width: 100%;
    padding: 10px;
    background-color: #f6ab59;
    border: none;
    border-radius: 5px;
    color: white;
    font-size: 16px;
  }
  </style>
  