<script setup>
import { computed, onBeforeUnmount, onMounted, ref, watch } from 'vue';
import { useGeolocation } from './composables/geolocation';
import { Loader } from '@googlemaps/js-api-loader';

const { coords } = useGeolocation();

const currPos = computed(() => ({
  lat: coords.value.latitude,
  lng: coords.value.longitude
}));

const otherPos = ref(null);

const loader = new Loader({
  apiKey: import.meta.env.VITE_GOOGLE_MAPS_API_KEY
})

const mapElement = ref(null);
const map = ref(null);
const currPosMarker = ref(null);
let mapClickListener = null;

onMounted(async () => {

  await loader.load()

  map.value = new google.maps.Map(mapElement.value, {
    center: currPos.value,
    zoom: 14
  });

  mapClickListener = map.value.addListener('click', ({ latLng: { lat, lng } }) => {
    otherPos.value = {
      lat: lat(),
      lng: lng()
    }
  });

  currPosMarker.value = new google.maps.Marker({
    position: currPos.value,
    title: "Current Location"
  });

  currPosMarker.value.setMap(map.value);

})

onBeforeUnmount(async () => {
  if(mapClickListener) mapClickListener.remove()
})

let line = null;
let otherPosMarker = null;
watch([map, currPos, otherPos], () => {

  if(line) line.setMap(null);
  if(otherPosMarker) otherPosMarker.setMap(null);

  if(map.value && currPos.value && otherPos.value){
    line = new google.maps.Polyline({
      path: [currPos.value, otherPos.value],
      map: map.value
    })

    otherPosMarker = new google.maps.Marker({
      position: otherPos.value,
      title: "Other Location",
      map: map.value
    })

  }
})

</script>

<template>
  <header class="shadow-sm">
    <nav class="navbar navbar-expand-sm navbar-light bg-light">
      <div class="container">
        <span class="navbar-brand">Google Maps in Vue.JS</span>
      </div>
    </nav>
  </header>
  <main>
    <div class="container py-4">
      <div class="d-flex flex-wrap">
        <output class="d-flex gap-3">
          <h5>Current Location</h5>

          <dl class="d-flex gap-2">
            <dt>Latitude: </dt>
            <dd>{{ currPos.lat.toFixed(4) }}</dd>
            <dt>Longitude: </dt>
            <dd>{{ currPos.lng.toFixed(4) }}</dd>
          </dl>
        </output>
        <output v-if="otherPos" class="d-flex gap-3">
          <h5>Other Location</h5>

          <dl class="d-flex gap-2">
            <dt>Latitude: </dt>
            <dd>{{ otherPos.lat.toFixed(4) }}</dd>
            <dt>Longitude: </dt>
            <dd>{{ otherPos.lng.toFixed(4) }}</dd>
          </dl>
        </output>
        <output v-else>
          <h5>Click on the map to view location</h5>
        </output>
      </div>

      <div ref="mapElement" class="bg-secondary rounded-4" style="width: 100%; height: 60vh"></div>
    </div>
  </main>
</template>
