<template>
    <div class="card-item">
        <div class="card">
            <img :src="props.character.image" alt="Character Image">
            <div class="card-info">
                <h2>{{ props.character.name }}</h2>
                <p></p>
                <span class="status">
                    <div v-if="props.character.status === 'Alive'" class="status-indicator alive"></div>
                    <div v-else-if="props.character.status === 'Dead'" class="status-indicator dead"></div>
                    {{ props.character.status }} - {{ props.character.species }}
                </span>
                <p class="label">Last known location:</p>
                <p>{{ props.character.location.name }}</p>
                <p class="label">First seen in:</p>
                <p>{{ episodeName }}</p>
            </div>
        </div>
    </div>
</template>

<script setup>
    const props = defineProps(['character'])

    import axios from 'axios';
    import { ref, onMounted } from "vue";

    const episodeName = ref("unknown");

    const getEpisode = async (page) => {
        try {
            const response = await axios.get(`${props.character.episode[0]}`);
            episodeName.value = response.data.name;
        } catch (error) {
            console.error('The request failed: ', error);
        }
    }

    onMounted(() => {
        getEpisode();
    })

</script>

<style scoped>
    .card {
        display: flex;
        background-color: #34495e;
        border-radius: 8px;
        overflow: hidden;
        width: 600px;
        color: white;
    }
    .card img {
        width: 200px;
        object-fit: cover;
    }
    .card-info {
        padding: 20px;
        flex: 1;
    }
    .card-info h2 {
        margin: 0;
        font-size: 24px;
        font-weight: bold;
    }
    .card-info p {
        margin: 10px 0;
        font-size: 16px;
    }
    .status {
        display: flex;
        align-items: center;
        font-weight: bold;
    }
    .status-indicator {
        width: 12px;
        height: 12px;
        border-radius: 50%;
        margin-right: 8px;
    }
    .alive {
        background-color: lawngreen;
    }
    .dead {
        background-color: coral;
    }
    .label {
        color: gray;
    }

</style>