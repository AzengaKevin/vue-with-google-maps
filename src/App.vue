<script setup>
import { computed } from 'vue';
import { useGeolocation } from './composables/geolocation';


const { coords } = useGeolocation();

const currPost = computed(() => ({
  lat: coords.value.latitude,
  lng: coords.value.longitude
}));

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
      <div class="d-flex">
        <output class="d-flex gap-3">
          <h5>Current Location</h5>
    
          <dl class="d-flex gap-2">
            <dt>Latitude: </dt>
            <dd>{{ currPost.lat }}</dd>
            <dt>Longitude: </dt>
            <dd>{{ currPost.lng }}</dd>
          </dl>
        </output>
      </div>
    </div>
  </main>
</template>
