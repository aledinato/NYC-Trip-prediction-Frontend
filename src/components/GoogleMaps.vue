<template>
  <div class="container">
    <div class="row">
      <div class="col-md-12 d-flex justify-content-center">
        <GoogleMap
          api-key="AIzaSyCn7KRHgbkl-PyJGfLBtWanmTRhUZJDULE"
          :center="{lat: pickup.lat, lng: pickup.lng}"
          :zoom="12"
          style="width:500px;height: 400px;"
          map-type-id="terrain"
          @click="mapClick"
        >
          <Marker
            :options="{position:{lat: pickup.lat, lng: pickup.lng}, icon: 'https://maps.google.com/mapfiles/ms/icons/green-dot.png'}"
          >
            <InfoWindow :position="{lat: pickup.lat, lng: pickup.lng}">
              Click here to get latitude and longitude!
            </InfoWindow>
          </Marker>

          <Marker
              :options="{position:{lat:  dropoff.lat, lng:  dropoff.lng}, icon: 'https://maps.google.com/mapfiles/ms/icons/red-dot.png'}"
          >
            <InfoWindow :position="{lat: dropoff.lat, lng: dropoff.lng}">
              Click here to get latitude and longitude!
            </InfoWindow>
          </Marker>
        </GoogleMap>
      </div>
      <div class="text-center"><p>Your are selecting <b :class="type==='pickup' ? 'text-success' : 'text-danger'">{{ this.type }}</b></p></div>
    </div>
  </div>
</template>

<script>
import {GoogleMap, Marker, InfoWindow} from "vue3-google-map"
export default {
  props: {
    type: {
      type: String,
      default: "pickup"
    },
    pickup: {
      type: Object,
      required: true
    },
    dropoff: {
      type: Object,
      required: true
    },
  },


  components: {
    GoogleMap,
    Marker,
    InfoWindow
  },

  methods: {
    mapClick(event) {
      this.$emit('mapClick', {
        'type': this.type,
        'lat': event.latLng.lat(),
        'lng': event.latLng.lng()
      })
    }
  },

  name: "GoogleMaps"
}
</script>

<style scoped>
  html,
  body {
    height: 100%;
    margin: 0;
    padding: 0;
  }
</style>