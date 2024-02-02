
<template>
  <main class="text-white container">
    <div class="pt-4 mb-8 relative">
      <input v-model="searchQuery" @input="getSearchResults" type="text" placeholder="Search for a city or state ..."
        class="py-2 px-1 w-full bg-transparent border-b focus:bg-weather-secondary focus:outline-none focus:shadow-md">
      <ul v-if="mapBoxSearchResults"
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]">
        <p v-if="searchError">
          Sorry, something's went wrong, please try again.
        </p>
        <p v-if="!searchError && mapBoxSearchResults.length === 0">
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li v-for="searchResult in mapBoxSearchResults" :key="searchResult.id" class="py-2 cursor-pointer"
            @click="previewCity(searchResult)">
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
import { ref } from "vue";
import axios from 'axios';
import { useRouter } from "vue-router";
import CityList from "@/components/CityList.vue";
import CityCardSkeleton from "@/components/CityCardSkeleton.vue";

const router = useRouter();
const mapBoxAPIKey = "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q"
const searchQuery = ref("");
const queryTimeOut = ref(null);
const mapBoxSearchResults = ref(null);
const searchError = ref(null);
const previewCity = (searchResult) => {
  // console.log(searchResult);
  const [city, state] = searchResult.place_name.split(",");
  router.push({
    name: "cityView",
    params: { state: state.replaceAll(" ", ""), city: city },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true
    }
  })
}

const getSearchResults = () => {
  clearTimeout(queryTimeOut.value);
  queryTimeOut.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapBoxAPIKey}&types=place`);
        mapBoxSearchResults.value = result.data.features;
        console.log(mapBoxSearchResults.value);
      } catch {
        searchError.value = true
      }
      return;
    }
    mapBoxSearchResults.value = null;
  }, 300);
}
</script>

