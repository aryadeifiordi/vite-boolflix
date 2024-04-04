<template>
    <div>
        <input type="text" v-model="searchQuery" placeholder="Cerca film o serie TV...">
        <button @click="searchMedia">Cerca</button>

        <div v-if="media.length > 0">
            <div v-for="item in media" :key="item.id">
                <img :src="getPosterUrl(item.poster_path)" :alt="item.title || item.name" v-if="item.poster_path">
                <h2>{{ item.title || item.name }}</h2>
                <p v-if="item.title">Titolo Originale: {{ item.original_title }}</p>
                <p v-else>Titolo Originale: {{ item.original_name }}</p>
                <p>Lingua: {{ getLanguageFlag(item.original_language) }} {{ item.original_language }}</p>
                <p>Voto: {{ item.vote_average }}</p>
                <hr>
            </div>
        </div>
        <div v-else-if="searchQuery && !isLoading">Nessun film o serie TV trovato.</div>
        <div v-if="isLoading">Caricamento...</div>
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

        media.value = [...movieData.results, ...tvData.results];
    } catch (error) {
        console.error('Errore durante la ricerca dei media:', error);
    } finally {
        isLoading.value = false;
    }
};

const getPosterUrl = (path) => {
    if (!path) return '';
    return `https://image.tmdb.org/t/p/w500${path}`;
};

const getLanguageFlag = (language) => {
    const flagMap = {
        'en': '🇬🇧',
        'it': '🇮🇹',

    };
    return flagMap[language.toLowerCase()] || '🏳️';
};
</script>