<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

const pexelsKey = '563492ad6f91700001000001c2b68c7f4154444aab64c87842c37233';
const horoscopeData = ref('');
const zodiacSign = ref('gemini');
const zodiacPhotos = ref('');
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
      .then(response => zodiacPhotos.value = response.data);
}

onMounted(async () => {
  getHoroscopes()
})

</script>

<template>
  <div class="flex justify-center pb-5">
    <div class="px-3" v-for="zodiac in horoscopes">
      <button @click="() => { zodiacSign = zodiac; getHoroscopes(); getPhotos() }">{{ zodiac }}</button>
    </div>
  </div>

  <div v-if="horoscopeData" class="px-10 lg:px-32 xl:px-80 pb-10">
    <div class="p-10 bg-gray-200 mb-2">
      <div class="flex justify-center">
        <div class="pb-5">
          <h1 class="text-6xl text-bolder tracking-widest">{{ zodiacSign }}</h1>
          <p class="uppercase text-xs tracking-wide">{{ horoscopeData.date_range }}</p>
        </div>
      </div>
      <p class="italic pb-2">Your horoscope for {{ horoscopeData.current_date }}</p>
      <p class="text-2xl">{{ horoscopeData.description }}</p>
      <div class="flex justify-around pt-5">
        <p>Your lucky number is <strong>{{ horoscopeData.lucky_number }}</strong></p>
        <p>Your lucky time is <strong>{{ horoscopeData.lucky_time }}</strong></p>
        <p>And your mood today is <strong>{{ horoscopeData.mood }}</strong></p>
      </div>
      <div class="flex justify-around">
        <p>Today you are most compatible with <strong>{{ horoscopeData.compatibility }}</strong></p>
        <p>Your color of the day is <strong>{{ horoscopeData.color }}</strong></p>
      </div>
    </div>
    <div class="flex justify-center pb-2">
      <div v-if="zodiacPhotos" v-for="photo in zodiacPhotos.photos.slice(0,4)" class="px-1">
        <img class="" alt="photo" :src="photo.src.medium" />
      </div>
    </div>
    <div class="flex justify-center">
      <div v-if="zodiacPhotos" v-for="photo in zodiacPhotos.photos.slice(5,9)" class="px-1">
        <img class="" alt="photo" :src="photo.src.medium" />
      </div>
    </div>
  </div>
</template>
