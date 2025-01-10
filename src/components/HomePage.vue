<template>
    <div class="home-page">
        <header>
            <h1>Welcome to AirBnb Camping</h1>
        </header>
        <section>
            <img src="../assets/airbnbLogo.png" alt="AirBnb Camping" class="camping-image" />
            <p>Discover the most beautiful campsites and unique accommodations around the world.</p>
        </section>
    </div>
</template>

<script>
    export default {
        name: 'HomePage'
    }
</script>

<style scoped>
    .home-page {
        text-align: center;
        padding: 20px;
    }

    header {
        background-color: #f8f8f8;
        padding: 20px;
        border-bottom: 1px solid #ddd;
    }

    h1 {
        margin: 0;
        font-size: 2em;
    }

    section {
        margin-top: 20px;
    }

    button {
        background-color: #ff5a5f;
        color: white;
        border: none;
        padding: 10px 20px;
        font-size: 1em;
        cursor: pointer;
        border-radius: 5px;
    }

    .camping-image {
        width: 100%;
        max-width: 400px;
        height: auto;
        border-radius: 10px;
    }

    button:hover {
        background-color: #e04e4e;
    }

    p {
        font-size: 25px;
    }
</style>