<template>
    <div class="flex py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer">
        <div class="flex flex-col flex-1">
            <h2 class="text-xl">{{ city.city }}</h2>
            <h2 >{{ city.state }}</h2>
        </div>
        <div class="flex flex-col gap-2">
            <p class="text-3xl self-end">
                {{ Math.round(city.weather.main.temp) }} &deg;
            </p>
            <div class="flex gap-1">
                 <span class="text-xs">
                    H: 
                    {{ Math.round(city.weather.main.temp_max) }} &deg;
                 </span>
            </div>
        </div>
    </div>
</template>

<script setup>
defineProps({
    city: {
        type: Object,
        default: ()=>{},
    }
})
</script>
