<template>
    <div class="card-list-container">
        <div class="card-list">
            <div v-for="characterData in charactersData" :key="characterData.id">
                <CardItem :character="characterData"/>
            </div>
        </div>
        <div class="pagination">
            <button @click="prevPage" :disabled="currentPage === 1">Previous</button>
            <span>Page {{ currentPage }} of {{ pagesCount }}</span>
            <button @click="nextPage" :disabled="currentPage === pagesCount">Next</button>
        </div>
    </div>
</template>

<script setup>
    import CardItem from '@/components/CardItem.vue';

    import axios from 'axios';
    import { BASE_URL } from "../config";
    import {ref, onMounted} from 'vue';

    const charactersData = ref(null);
    const pagesCount = ref(1);
    const currentPage = ref(1);

    const prevPage = async () => {
        currentPage.value--;
        getCharacters(currentPage.value);
    }
    const nextPage = async () => {
        currentPage.value++;
        getCharacters(currentPage.value);
    }

    const getCharacters = async (page) => {
        try {
            const response = await axios.get(`${BASE_URL}/character/?page=${page}`);
            charactersData.value = response.data.results;
            pagesCount.value = response.data.info.pages;
        } catch (error) {
            console.error('The request failed: ', error);
        }
    }

    onMounted(() => {
        getCharacters(1);
    })

</script>

<style scoped>
    .card-list-container {
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .card-list-container {
        padding: 16px;
    }
    .card-list {
        display: flex;
        flex-wrap: wrap;
        gap: 16px;
        justify-content: center;
        align-items: center;
    }
    .card-list > div {
        flex: 1 1 calc(33.333% - 32px);
        box-sizing: border-box;
    }


    .pagination {
        margin-top: 20px;
        display: flex;
        justify-content: center;
        align-items: center;
    }
    .pagination button {
        background-color: #34495e;
        color: white;
        border: none;
        border-radius: 8px;
        padding: 10px 20px;
        font-size: 16px;
        cursor: pointer;
        margin: 0 5px;
        transition: background-color 0.3s ease;
    }
    .pagination button:disabled {
        background-color: #7f8c8d;
        cursor: not-allowed;
    }
    .pagination button:hover:not(:disabled) {
        background-color: #2c3e50;
    }

</style>