<template>
    <div>
        <div v-for="characterData in charactersData" >
            <CardItem :character="characterData"/>
        </div>
    </div>
</template>

<script setup>
    import CardItem from '@/components/CardItem.vue';

    import axios from 'axios';
    import { BASE_URL } from "../config";
    import {ref, onMounted} from 'vue';

    const charactersData = ref(null);
    const currentPage = ref(0);

    const getCharacters = async (page) => {
        try {
            const response = await axios.get(`${BASE_URL}/character/?page=${page}`);
            charactersData.value = response.data.results;
        } catch (error) {
            console.error('The request failed: ', error);
        }
    }

    onMounted(() => {
        getCharacters(1);
    })

</script>

<style scoped>

</style>