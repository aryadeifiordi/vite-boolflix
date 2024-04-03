<template>
    <div>
        <input type="text" v-model="searchQuery" placeholder="Cerca film...">
        <button @click="searchMovies">Cerca</button>

        <div v-if="movies.length > 0">
            <div v-for="movie in movies" :key="movie.id">
                <h2>{{ movie.title }}</h2>
                <p>Titolo Originale: {{ movie.original_title }}</p>
                <p>Lingua: {{ movie.original_language }}</p>
                <p>Voto: {{ movie.vote_average }}</p>
            </div>
        </div>
        <div v-else-if="searchQuery && !isLoading">Nessun film trovato.</div>
        <div v-if="isLoading">Caricamento...</div>
    </div>
</template>

<script setup>
import { ref } from 'vue';

const apiKey = 'f7bf2a5808b06a8304a08eb2f47da799';
const searchQuery = ref('');
const movies = ref([]);
const isLoading = ref(false);

const searchMovies = async () => {
    if (!searchQuery.value) return;
    isLoading.value = true;
    try {
        const response = await fetch(`https://api.themoviedb.org/3/search/movie?api_key=f7bf2a5808b06a8304a08eb2f47da799&query=${searchQuery.value}`);
        const data = await response.json();
        movies.value = data.results;
    } catch (error) {
        console.error('Errore durante la ricerca dei film:', error);
    } finally {
        isLoading.value = false;
    }
};
</script>