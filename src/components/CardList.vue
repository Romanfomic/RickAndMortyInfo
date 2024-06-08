<template>
    <div class="card-list-container">
        <div class="filter-section">
            <input type="text" v-model="filterName" placeholder="Filter by name" class="filter-input"/>
            <select v-model="filterStatus" class="filter-select">
                <option value="">All</option>
                <option value="Alive">Alive</option>
                <option value="Dead">Dead</option>
                <option value="Unknown">Unknown</option>
            </select>
            <button @click="applyFilters" class="apply-button">Apply</button>
        </div>
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
    const filterName = ref("");
    const filterStatus = ref("");

    const applyFilters = async () => {
        if (filterStatus.value === "All") { filterStatus.value = ""; }
        getCharacters(1, filterName.value, filterStatus.value)
    }
    const prevPage = async () => {
        currentPage.value--;
        getCharacters(currentPage.value, filterName.value, filterStatus.value);
    }
    const nextPage = async () => {
        currentPage.value++;
        getCharacters(currentPage.value, filterName.value, filterStatus.value);
    }

    const getCharacters = async (page=0, name="", status="") => {
        let queryString = `${BASE_URL}/character/?`;
        if(page !== 0) {
            queryString += `page=${page}`;
        }
        if (name !== "") {
            queryString += `&name=${name}`;
        }
        if (status !== "") {
            queryString += `&status=${status}`;
        }
        try {
            const response = await axios.get(queryString);
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
    .filter-section {
        display: flex;
        align-items: center;
        margin-bottom: 20px;
    }
    .filter-input, .filter-select, .apply-button {
        margin-right: 10px;
    }
    .filter-input, .filter-select {
        padding: 8px;
        border-radius: 5px;
        border: 1px solid #ccc;
        font-size: 16px;
    }
    .apply-button {
        background-color: #34495e;
        color: white;
        border: none;
        border-radius: 5px;
        padding: 8px 16px;
        font-size: 16px;
        cursor: pointer;
        transition: background-color 0.3s ease;
    }
    .apply-button:hover {
        background-color: #2c3e50;
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