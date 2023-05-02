<template>
  <header>
    <div class="wrapper">
      <GoogleMaps @mapClick="update_position" :pickup="get_position('pickup')" :dropoff="get_position('dropoff')" :type="type"></GoogleMaps>
    </div>
  </header>

  <main>
    <div>
      <ModalResult ref="modal" @close="close" :result="this.result" :pickup_datetime="item.datetime"></ModalResult>
    </div>
    <form @submit.prevent="predict()">
      <div class="form-group">
        <label>Vendor id</label>
        <input type="number" class="form-control" min="1" max="2" placeholder="Vendor id" v-model="item.vendor_id" />
      </div>
      <div class="form-group">
        <label>Pickup latitude</label>
        <input step="0.000000000000001" @focus="triggerType('pickup')" min="40.55" max="40.925" type="number" class="form-control" placeholder="Pickup latitude" v-model="pickup.lat" />
      </div>
      <div class="form-group">
        <label>Pickup longitude</label>
        <input step="0.000000000000001"  @focus="triggerType('pickup')" min="-74.05" max="-73.7" type="number" class="form-control" placeholder="Pickup longitude" v-model="pickup.lng" />
      </div>
      <div class="form-group">
        <label>Dropoff latitude</label>
        <input step="0.000000000000001"  @focus="triggerType('dropoff')" min="40.55" max="40.925" type="number" class="form-control" placeholder="Dropoff latitude" v-model="dropoff.lat" />
      </div>
      <div class="form-group">
        <label>Dropoff longitude</label>
        <input step="0.000000000000001"  @focus="triggerType('dropoff')" min="-74.05" max="-73.7" type="number" class="form-control" placeholder="Dropoff longitude" v-model="dropoff.lng" />
      </div>
      <div class="form-group">
        <label>Datetime</label>
        <input type="datetime-local" class="form-control" v-model="item.datetime" />
      </div>
      <button type="submit" class="btn btn-primary mt-3">Predict</button>
    </form>
  </main>
</template>

<script>
import GoogleMaps from './components/GoogleMaps.vue'
import axios from "axios";
import ModalResult from '@/components/ModalResult.vue';

export default {

  components: {
    GoogleMaps,
    axios,
    ModalResult
  },

  data(){
    return {
      result: null,
      type: "pickup",
      item: {
        vendor_id: 1,
        pickup_latitude: 40.7127837,
        pickup_longitude: -74.0059413,
        dropoff_latitude: 40.75595398566725,
        dropoff_longitude: -73.97350155045734,
        datetime: "2016-01-01T00:00"
      }
    }
  },

  computed: {
    pickup: {
      get() {
        return {lat: this.item.pickup_latitude, lng: this.item.pickup_longitude}
      },
      set(value) {
        this.item.pickup_latitude = value.lat
        this.item.pickup_longitude = value.lng
      }
    },
    dropoff: {
      get() {
        return {lat: this.item.dropoff_latitude, lng: this.item.dropoff_longitude}
      },
      set(value) {
        this.item.dropoff_latitude = value.lat
        this.item.dropoff_longitude = value.lng
      }
    }
  },

  methods: {
    close(){
      this.result=null
      $(this.$refs.modal.$el).modal("hide")
    },
    triggerType(type){
      this.type=type
    },

    async predict() {
      this.item.datetime = this.item.datetime.replace("T", " ")
      let data = JSON.stringify(this.item)
      $(this.$refs.modal.$el).modal("show")//apro il modal
      let response = await axios.post("http://localhost:8000/predict-taxi-trip", data, {
        headers: {
          'Content-Type': 'application/json'
        }
      }).catch((error) => {
        console.log(error)
        alert("Error message: " + error.message)
      })
      this.result = Math.ceil(response.data.result / 60)
    },

    update_position(item) {
      if (item.type === "pickup") {
        this.pickup = item
      } else if (item.type === "dropoff") {
        this.dropoff = item
      }
    },

    get_position(type) {
      if (type === "pickup") {
        return {
          lat: this.item.pickup_latitude,
          lng: this.item.pickup_longitude
        }
      } else if (type === "dropoff") {
        return {
          lat: this.item.dropoff_latitude,
          lng: this.item.dropoff_longitude
        }
      }
    },
  },
}
</script>

<style scoped>
header {
  line-height: 1.5;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
