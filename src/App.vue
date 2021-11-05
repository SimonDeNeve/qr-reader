<template>
    <div id="app">
      <div class="col-md-4">

        <div class="card" style="width: 18rem;">
          <qrcode-stream @decode="onDecode" @init="onInit" />
          <!-- <div class="loading-indicator" v-if="loading">
            Loading...
          </div> -->
            <div class="card-body">
              <p class="card-text">Please scan the qrcode event registration that we have sent via your email.</p>
                <hr>
                  <div class="alert" :class="alert" role="alert">
                    {{ status }}
                  </div>
                </div>
              </div>
            </div>
          </div>
</template>

<script>

import { QrcodeStream } from 'vue-qrcode-reader'
import { Geolocation } from '@capacitor/geolocation';

export default {
  name: 'app',
  data() {
    return {
      loading: false,
      error: '',
      status: 'Webcam ready!',
      alert: 'alert-warning',
      result: ''
    }
  },
  components: { QrcodeStream, Geolocation },
  methods: {
    const printCurrentPosition = async () => {
      const coordinates = await Geolocation.getCurrentPosition();
      console.log('Current position:', coordinates);
    },
    async onDecode (result) {
      this.result = result
    },
    async onInit (promise) {
      this.loading = true
      try {
        await promise
      } catch (error) {
        if (error.name === 'NotAllowedError') {
          this.alert = 'alert-danger'
          this.status = "ERROR: you need to grant camera access permisson"
        } else if (error.name === 'NotFoundError') {
          this.alert = 'alert-danger'
          this.status = "ERROR: no camera on this device"
        } else if (error.name === 'NotSupportedError') {
          this.alert = 'alert-danger'
          this.status = "ERROR: secure context required (HTTPS, localhost)"
        } else if (error.name === 'NotReadableError') {
          this.alert = 'alert-danger'
          this.status = "ERROR: is the camera already in use?"
        } else if (error.name === 'OverconstrainedError') {
          this.alert = 'alert-danger'
          this.status = "ERROR: installed cameras are not suitable"
        } else if (error.name === 'StreamApiNotSupportedError') {
          this.alert = 'alert-danger'
          this.status = "ERROR: Stream API is not supported in this browser"
        }
        else {
          this.error = `ERROR: Camera error (${error.name})`;
        }
      } finally {
        this.loading = false
      }
    }
  }
}
</script>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
