<template>
  <div class="w-full h-auto flex flex-col">
    
    <div class=" w-full flex flex-col relative bg-hero-pattern bg-cover justify-center items-center">
      <div class="w-4/12 py-10 pb-20 m-auto justify-center items-center text-center sm:w-10/12 sm:pb-6">
        <h1 class="text-2xl mb-4 text-gray-50">IP Address Tracker</h1>
        <div class="flex justify-center items-center">
          <input v-model="ipAddress" class="flex-1 px-4 py-2 rounded-tl-md rounded-bl-md focus:outline-none" placeholder="Search any ip address" />
          <i @click="fetchIpAddressInfo" class="px-4 py-3 bg-black text-white fas fa-chevron-right rounded-tr-md rounded-br-md"></i>
        </div>
      </div>

      <div v-if="ipInfo" class="w-6/12 z-20 aboslute -bottom-5 sm:w-10/12">
        <div class="w-full m-auto bg-white rounded-md flex justify-between py-4 px-8 shadow-xl sm:flex-col sm:text-center">
          <div class="sm:my-2">
            <p class="text-gray-400 text-sm ">IP Address</p>
            <span class="text-lg mt-2 font-bold">{{ipInfo.ip}}</span>
          </div>
          <div class="sm:my-2">
            <p class="text-gray-400 text-sm ">Location</p>
            <span class="text-lg mt-2 font-bold">{{ipInfo.location}}</span>
          </div>
          <div class="sm:my-2">
            <p class="text-gray-400 text-sm ">Timezone</p>
            <span class="text-lg mt-2 font-bold">{{ipInfo.timezone}}</span>
          </div>
          <div class="sm:my-2">
            <p class="text-gray-400 text-sm ">ISP</p>
            <span class="text-lg mt-2 font-bold">{{ipInfo.isp}}</span>
          </div>

      </div>
    </div>
    </div>
    

    <div id="map" class="-mt-10 z-10 w-full h-full">

    </div>

  </div>
</template>

<script setup>
import leaflet from 'leaflet'
import axios from 'axios'
import { onMounted, ref } from 'vue'

const ipAddress = ref("8.8.8.8")
const ipInfo = ref(null) 


let leafletMap;

const fetchIpAddressInfo = async () => {
  try {
    const res = await axios.get(`https://geo.ipify.org/api/v2/country,city?apiKey=at_ttunrnqLLMYpvVl2jjd2ZVRZLW48m&ipAddress=${ipAddress.value}`)

    const infos = res.data
    console.log(infos)
    ipInfo.value = {
      ip: infos.ip,
      isp: infos.isp,
      location: infos.location.region,
      lat: infos.location.lat,
      lng: infos.location.lng,
      timezone: infos.location.timezone
    }
    leaflet.marker([ipInfo.value.lat, ipInfo.value.lng]).addTo(leafletMap);
    leafletMap.setView([ipInfo.value.lat, ipInfo.value.lng], 13);
  } catch (err){
    console.log(err.message)
  }
}

onMounted(() => {
  
  leafletMap = leaflet.map('map').setView([51.505, -0.09], 13);

  leaflet.tileLayer('https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoiaW1yYW5oMjEiLCJhIjoiY2t3cmFrNHB1MGRiaDMxbHNrN2h2bWhqOSJ9.-w-lWFRVhqxopLIuVja_lw', {
    attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
    maxZoom: 18,
    id: 'mapbox/streets-v11',
    tileSize: 512,
    zoomOffset: -1,
    accessToken: 'pk.eyJ1IjoiaW1yYW5oMjEiLCJhIjoiY2t3cmFrNHB1MGRiaDMxbHNrN2h2bWhqOSJ9.-w-lWFRVhqxopLIuVja_lw'
  }).addTo(leafletMap);
  var marker = leaflet.marker([51.5, -0.09]).addTo(leafletMap);
  fetchIpAddressInfo()
})
</script>

<style lang="scss" scoped>

</style>
