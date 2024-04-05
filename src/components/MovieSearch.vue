<template>
    <div>
        <header>
            <img src="../img/Netflix-logo.png" alt="Logo" class="logo">
            <input type="text" v-model="searchQuery" placeholder="Cerca film o serie TV..." @keyup.enter="searchMedia">
            <button @click="searchMedia">Cerca</button>
        </header>

        <div class="results">

            <div v-for="item in media" :key="item.id" class="card" @mouseover="showDetails(item)"
                @mouseleave="hideDetails(item)">
                <img :src="getPosterUrl(item.poster_path)" :alt="item.title || item.name" class="poster">
                <div class="details" v-if="item.showDetails">
                    <h2>{{ item.title || item.name }}</h2>
                    <p v-if="item.title">Titolo Originale: {{ item.original_title }}</p>
                    <p v-else>Titolo Originale: {{ item.original_name }}</p>
                    <p>Lingua: {{ getLanguageFlag(item.original_language) }} {{ item.original_language }}</p>
                    <p>Voto: {{ getRatingStars(item.vote_average) }}</p>
                    <p>{{ item.overview }}</p>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue';

const apiKey = 'f7bf2a5808b06a8304a08eb2f47da799';
const searchQuery = ref('');
const media = ref([]);
const isLoading = ref(false);

const searchMedia = async () => {
    if (!searchQuery.value) return;
    isLoading.value = true;
    try {
        const movieResponse = await fetch(`https://api.themoviedb.org/3/search/movie?api_key=${apiKey}&query=${searchQuery.value}`);
        const movieData = await movieResponse.json();

        const tvResponse = await fetch(`https://api.themoviedb.org/3/search/tv?api_key=${apiKey}&query=${searchQuery.value}`);
        const tvData = await tvResponse.json();

        media.value = [...movieData.results, ...tvData.results].map(item => ({
            ...item,
            showDetails: false
        }));
    } catch (error) {
        console.error('Errore durante la ricerca dei media:', error);
    } finally {
        isLoading.value = false;
    }
};

const getPosterUrl = (path) => {
    if (!path) return '';
    return `https://image.tmdb.org/t/p/w342${path}`;
};

const getLanguageFlag = (language) => {
    const flagMap = {
        'en': '🇬🇧',
        'it': '🇮🇹',
    };
    return flagMap[language.toLowerCase()] || '🏳️';
};

const getRatingStars = (rating) => {
    const numStars = Math.ceil(rating / 2);
    return '⭐️'.repeat(numStars);
};

const showDetails = (item) => {
    item.showDetails = true;
};

const hideDetails = (item) => {
    item.showDetails = false;
};
</script>

<style scoped>
header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem;
    background-color: black;

}

.logo {
    height: 50px;
}

input {
    padding: 0.5rem;
    border: 1px solid #ccc;
    border-radius: 5px;
}

button {
    padding: 0.5rem 1rem;
    background-color: red;
    color: #fff;
    border: none;
    border-radius: 5px;
    cursor: pointer;
}

.results {
    display: flex;
    flex-wrap: wrap;
}

.card {
    width: 200px;
    margin: 1rem;
    padding: 0.5rem;
    border: 1px solid #ccc;
    border-radius: 5px;
    transition: transform 0.3s ease;
}

.card:hover {
    transform: scale(1.05);
}

.poster {
    width: 100%;
    border-radius: 5px;
}

.details {
    display: none;
    padding-top: 0.5rem;
}

.card:hover .details {
    display: block;
}
</style>
