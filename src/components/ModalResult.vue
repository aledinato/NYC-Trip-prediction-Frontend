<template>
  <div class="modal fade" tabindex="-1" role="dialog">
    <div class="modal-dialog" role="document">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Result</h5>
          <button @click="$emit('close')" type="button" class="close" data-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <div class="modal-body d-flex justify-content-center">
          <div id="dishwasher" class="loader"></div>
          <div id="result">
            <h6  class="text-center">Your trip duration is about <b>{{result}} minutes</b></h6>
            <h6  class="text-center">Your dropoff time is about <b>{{getDropoffDate()}}</b></h6>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    result: {
      type: Number,
      default: null
    },
    pickup_datetime: {
      type: String,
      default: null
    },
  },
  mounted() {
    $('#dishwasher').show();
    $('#result').hide();
  },

  watch: {
    result: function (newResult) {
      if (newResult === null) {
        console.log(newResult);
        $('#dishwasher').show();
        $('#result').hide();
      }else{
        $('#dishwasher').hide();
        $('#result').show();
      }
    }
  },

  methods: {
    getDropoffDate(){
      let date = new Date(this.pickup_datetime);
      date.setMinutes(date.getMinutes() + this.result);
      return date.toLocaleString();
    }
  },
  name: "ModalResult"
}
</script>

<style scoped>

</style>