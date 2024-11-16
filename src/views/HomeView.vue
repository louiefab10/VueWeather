<template>
  <main class="container text-black">
    <div class="pt-4 mb-8 relative">
      <input type="text" v-model="searchQuery" @input="getSearchResults" placeholder="Search for a City" class="py-2 px-1 w-full bg-transparent border-b focus:border-coral focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]">
      <ul v-if="mapboxSearchResults" class="absolute bg-white text-coral w-full shadow-md py-2 px-1 top-[66px]">
        <p v-if="searchError">Sorry, something went wrong, please try again.</p>
        <p v-if="!serverError && mapboxSearchResults.length === 0">No results found, please try a different city.</p>
        <template v-else>
          <li v-for="searchResult in mapboxSearchResults" :key="searchResult.id" class="py-2 cursor-pointer" @click="previewCity(searchResult)">
            {{ searchResult.place_name }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList/>
        <template #fallback>
          <CityCardSkeleton/>
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import {ref} from "vue";
import axios from "axios";
import {useRouter} from "vue-router";
import CityList from "../components/CityList.vue";
import CityCardSkeleton from "../components/CityCardSkeleton.vue";
const router = useRouter();
const previewCity = (searchResult) =>{
  const [city, state] = searchResult.place_name.split(',');
  console.log(searchResult);
  router.push({
    name: "cityView",
    params: {state: state, city: city},
    query:{
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true,
    }
  });
}; 

const searchQuery = ref("");
const mapboxAPIKey = import.meta.env.VITE_mapbox_api_key;
const queryTimeout = ref(null);
const mapboxSearchResults = ref(null);
const searchError = ref(null);

const getSearchResults = () =>{
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if(searchQuery.value !== ''){
      try{
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`);
        mapboxSearchResults.value = result.data.features;
      }
      catch{
          searchError.value = true;
      }
      return;
    }
    mapboxSearchResults.value = null;
  }, 300);
}
</script>
