<template>
<header class="sticky top-0 bg-weather-primary shadow-lg">
    <nav class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6">
        <RouterLink :to="{name: 'home'}">
            <div class="flex items-center gap-3">
                <i class="fa-solid fa-cloud-sun text-2xl"></i>
                <p class="text-2xl font-Source_Code_Pro">Weather, weather lang yan</p>
            </div>
        </RouterLink>
        <div class="flex gap-3 flex-1 justify-end">
            <i class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="toggleModal"></i>
            <i class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="addCity" v-if="route.query.preview"></i>
        </div>
        <BaseModal :modalActive="modalActive" @close-modal="toggleModal">
            <div class="font-Source_Code_Pro text-black">
                <h1 class="text-2xl mb-1">About:</h1>
                <p class="mb-4">
                    This website lets you track the current and upcoming weather forecast in the area of your choosing.
                </p>
                <h2 class="text-2xl">How does it work?</h2>
                <ol class="list-decimal list-inside mb-4">
                    <li>Search for your area by entering the name into the search box.</li>
                    <li>Select a city from the results, this will then take you the current weather in that area.</li>
                    <li>Traxk the city by clicking on the "+" icon in the top right. This will save the city to view at a later time.</li>
                </ol>
                <h2 class="text-2xl">Removing a city</h2>
                <p>If you no longer wish to track a city, simply select the city within the home page. At the bottom of the page, there will be an option to delete the city.</p>
            </div>
        </BaseModal>
    </nav>
</header>
</template>

<script setup>
import {ref} from 'vue';
import {uid} from 'uid';
import { RouterLink, useRoute, useRouter } from 'vue-router'; 
import BaseModal from './BaseModal.vue';
const savedCities = ref([]);
const route = useRoute();
const router = useRouter();
const addCity = () => {
    if(localStorage.getItem('savedCities')){
        savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
    }

    const locationObj={
        id: uid(),
        state: route.params.state,
        city: route.params.city,
        coords:{
            lat: route.query.lat,
            lng: route.query.lng,
        },
    };
    savedCities.value.push(locationObj);
    localStorage.setItem('savedCities', JSON.stringify(savedCities.value));
    let query = Object.assign({}, route.query);
    delete query.preview;
    query.id = locationObj.id;
    router.replace({query});
};

const modalActive = ref(null);
const toggleModal = () => {
    modalActive.value = !modalActive.value;
};
</script>
