<template>
  <div id="app">
    <nav>
      <ul class="nav-list">
        <li v-for="(page, index) in pages" :key="index" @click="changePage(page)" :class="{ active: activePage === page }">
          {{ page }}
        </li>
      </ul>
    </nav>

    <hr>

    <div>
      <HomePage v-if="activePage == 'home'" />
    </div>
  </div>
</template>

<script>
import HomePage from './components/HomePage.vue';

export default {
  name: 'App',
  components: {
    HomePage,
  },
  data() {
    return {
      activePage: 'home',
      pages: ['home']
    }
  },
  methods: {
    changePage(page) {
      console.log("Changing page to: " + page);
      this.activePage = page;
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
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
</style>
