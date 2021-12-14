<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

const pexelsKey = '563492ad6f91700001000001c2b68c7f4154444aab64c87842c37233';
const horoscopeData = ref('');
const zodiacSign = ref('gemini');
const zodiacPhotosColor = ref('');
const zodiacPhotosMood = ref('');
const headers = { Authorization: pexelsKey };
const horoscopes = ['capricorn', 'aquarius', 'pisces', 'aries', 'taurus', 'gemini', 'cancer', 'leo', 'virgo', 'libra', 'scorpio', 'sagittarius'];

const getHoroscopes = async () => {
  await axios.post('https://aztro.sameerkumar.website/?sign=' + zodiacSign.value + '&day=today')
      .then(response => {
        horoscopeData.value = response.data
        getPhotos()
      })
}

const getPhotos = async () => {
  await axios.get('https://api.pexels.com/v1/search?query=' + horoscopeData.value.color, { headers })
      .then(response => zodiacPhotosColor.value = response.data);

  await axios.get('https://api.pexels.com/v1/search?query=' + horoscopeData.value.mood, { headers })
      .then(response => zodiacPhotosMood.value = response.data);
}

onMounted( () => {
  getHoroscopes()
})

</script>

<template>
  <div class="flex justify-center pb-5 flex-wrap pt-5 md:pt-7">
    <div class="px-5 md:px-3" v-for="zodiac in horoscopes">
      <button class="text-sm md:text-base" :class="zodiacSign === zodiac ? 'font-black' : 'font-normal'" @click="() => { zodiacSign = zodiac; getHoroscopes(); getPhotos() }">{{ zodiac }}</button>
    </div>
  </div>

  <div v-if="horoscopeData" class="px-5 lg:px-32 xl:px-80 pb-10">
    <div class="p-7 md:p-10 bg-gray-200 md:mb-1">
      <div class="flex justify-center">
        <div class="border border-gray-400 px-5 py-2 mb-3 lg:mb-5">
          <h1 class="text-5xl md:text-6xl tracking-widest pb-2">{{ zodiacSign }}</h1>
          <p class="uppercase text-xs tracking-wide">{{ horoscopeData.date_range }}</p>
        </div>
      </div>
      <p class="text-xs md:text-normal italic pb-2">Your horoscope for {{ horoscopeData.current_date }}</p>
      <p class="text-xl md:text-2xl font-thin">{{ horoscopeData.description }}</p>
      <div class="flex justify-around flex-wrap pt-5">
        <p>Your lucky number is <strong>{{ horoscopeData.lucky_number }}</strong></p>
        <p>Your lucky time is <strong>{{ horoscopeData.lucky_time }}</strong></p>
        <p>Your your mood today is <strong>{{ horoscopeData.mood }}</strong></p>
      </div>
      <div class="flex justify-around flex-wrap">
        <p>You are most compatible with <strong>{{ horoscopeData.compatibility }}</strong></p>
        <p>Your color of the day is <strong>{{ horoscopeData.color }}</strong></p>
      </div>
    </div>
    <div class="flex justify-center pb-2 flex-wrap md:flex-nowrap -mx-2 md:-mx-1">
      <div v-if="zodiacPhotosColor" v-for="photo in zodiacPhotosColor.photos.slice(0,4)" class="p-2 md:p-1 pb-0 md:pb-0">
        <img class="" alt="photo" :src="photo.src.medium" />
      </div>
    </div>
    <div class="flex justify-center flex-wrap md:flex-nowrap -mx-2 md:-mx-1">
      <div v-if="zodiacPhotosMood" v-for="photo in zodiacPhotosMood.photos.slice(0,4)" class="p-2 md:p-1 pt-0 md:pt-0">
        <img class="" alt="photo" :src="photo.src.medium" />
      </div>
    </div>
  </div>
</template>
