<script
src="https://maps.googleapis.com/maps/api/js?key=AIzaSyDcuSoUhAyNDtR-7FzCaX94iGRTK4oV2Ms&libraries=places"
async
defer
></script> 
<template>
  <v-app class="wrapper">
          <v-row>
              <header class="navigation">
                  <img src="./assets/logo.png" height="40px"  alt="Description of the image" class="my-image" />
              </header>
          </v-row>
        <v-row>
          <v-col cols="4">
            <v-form ref="routeForm" v-model="valid" lazy-validation class="form">
                    <h2 class="title">Route configuration</h2>
                  <v-row>
                    <v-col cols="4">
                      <h3 class="label">Loading</h3>
                      <v-select
                        v-model="loadingCountry"
                        :items="countries"
                        label="Country"
                        :rules="[rules.required]"
                        class="custom-select"
                        outlined
                      >
                    </v-select>
                    </v-col>
                    <v-col cols="8">
                      <h4 class="label guid">Add loading spot</h4>
                      <v-text-field
                        v-model="loadingAddress"
                        label="Address"
                        :rules="[rules.required]"
                        ref="loadingAddressInput"
                        class="custom-input"
                        outlined
                      />
                    </v-col>
                  </v-row>
                  
                  <v-row>
                    <v-col cols="4">
                      <h3 class="label">Unloading</h3>
                      <v-select
                        v-model="unloadingCountry"
                        :items="countries"
                        label="Country"
                        :rules="[rules.required]"
                        class="custom-select"
                        outlined
                      />
                    </v-col>
                    <v-col cols="8">
                      <h4 class="label guid">Add unloading spot</h4>
                      <v-text-field
                        v-model="unloadingAddress"
                        label="Address"
                        :rules="[rules.required]"
                        ref="unloadingAddressInput"
                        class="custom-input"
                        outlined
                      />
                    </v-col>
                  </v-row>

                  <v-row>
                    <v-col>
                      <h3>Avoid Countries</h3>
                      <v-select
                        v-model="avoidCountries"
                        :items="countries"
                        label="Avoid Countries"
                        multiple
                        class="custom-select"
                        outlined
                      />
                    </v-col>
                  </v-row>

                  <v-row>
                    <v-col>
                      <v-btn :disabled="!valid" @click="calculateRoute" class="submit">
                        {{ calculating ? 'Calculating...' : 'Calculate Route' }}
                      </v-btn>
                    </v-col>
                  </v-row>
                    <w-row v-if="routeDetails">
                        <v-col>
                            <h3 class="route_title">Route details</h3>
                        </v-col>
                        <v-row class="route_details">
                          <v-col>
                            <v--figure class="content">
                              <v-figure class="elem">
                                <img src="./assets/distance.png"  alt="warning" width="" />
                              <span> Distance:</span>
                              </v-figure>
                                <h2 class="indicator">{{ routeDetails.distance }} km </h2>
                              </v--figure>
                          </v-col>
                          <v-col>
                            <v--figure class="content" >
                              <v-figure class="elem">
                                <img src="./assets/time.png"  alt="warning" width="" />
                              <span> Driving Time</span>
                              </v-figure> 
                                <h2 class="indicator">{{ routeDetails.time }}</h2>
                            </v--figure>
                          </v-col>
                      </v-row>
                    </w-row>
                  
                  <v-row v-if="noRouteFound" class="error_message">
                    <h3>Route details</h3>
                      <p>
                        <img src="./assets/warning.png"  alt="warning" width="18px" />
                        {{ noRouteMessage }}</p>
                  </v-row>
                </v-form>
          </v-col>  
        <v-col cols="8">
              <MapComponent ref="mapComponent" />
        </v-col>
      </v-row> 
  </v-app>
</template>



<script>
import MapComponent from './components/MapComponent.vue';
import axios from 'axios';
import polyline from '@mapbox/polyline';

export default {
  name: 'App',
  components: {
    MapComponent,
  },
  data() {
    return {
      loadingCountry: '',
      unloadingCountry: '',
      loadingAddress: '',
      unloadingAddress: '',
      avoidCountries: [],
      loadingAddressLat: null,
      loadingAddressLng: null,
      unloadingAddressLat: null,
      unloadingAddressLng: null,
      countries: ['DE', 'CH', 'AT', 'NL', 'BE', 'FR', 'ES', 'PL'],
      valid: false,
      calculating: false,
      routeDetails: null,
      noRouteFound: false,
      noRouteMessage: 'No route was found for the given locations.',
      rules: {
        required: value => !!value || 'Required',
      },
    };
  },
  mounted() {
    this.initAutocomplete();
  },
  methods: {
    initAutocomplete() {
      const loadingInput = this.$refs.loadingAddressInput.$el.querySelector('input');
      const unloadingInput = this.$refs.unloadingAddressInput.$el.querySelector('input');

      const loadingAutocomplete = new google.maps.places.Autocomplete(loadingInput);
      const unloadingAutocomplete = new google.maps.places.Autocomplete(unloadingInput);

      setTimeout(() => {
          loadingInput.placeholder = 'Address';
          unloadingInput.placeholder = 'Address';
        }, 100);

      loadingAutocomplete.addListener('place_changed', () => {
        const place = loadingAutocomplete.getPlace();
        if (place.geometry) {
          this.loadingAddressLat = place.geometry.location.lat();
          this.loadingAddressLng = place.geometry.location.lng();
          this.loadingAddress = place.formatted_address;
        }
      });

      unloadingAutocomplete.addListener('place_changed', () => {
        const place = unloadingAutocomplete.getPlace();
        if (place.geometry) {
          this.unloadingAddressLat = place.geometry.location.lat();
          this.unloadingAddressLng = place.geometry.location.lng();
          this.unloadingAddress = place.formatted_address;
        }
      });
    },

    async calculateRoute() {
  const isValid = await this.$refs.routeForm.validate();
  if (!isValid) return;

  if (!this.loadingAddressLat || !this.unloadingAddressLat) {
    this.noRouteFound = true;
    this.noRouteMessage = 'Please enter valid loading and unloading addresses.';
    return;
  }

  this.calculating = true;
  this.noRouteFound = false;
  this.routeDetails = null;

  const payload = {
    coordinates: [
      [this.loadingAddressLng, this.loadingAddressLat],
      [this.unloadingAddressLng, this.unloadingAddressLat],
    ],
    options: {
      avoid_countries: this.avoidCountries,
      avoid_features: ['tollways', 'ferries'],
    },
  };

  try {
    const response = await axios.post('https://maps.zipmend.com/ors/v2/directions/driving-hgv', payload, {
      timeout: 10000,
    });

    const { routes } = response.data;

    if (routes && routes.length) {
      const route = routes[0];
      this.routeDetails = {
        distance: (route.summary.distance / 1000).toFixed(2),
        time: this.formatDrivingTime(route.summary.duration),
      };

      const decodedGeometry = polyline.decode(route.geometry);

      this.$refs.mapComponent.displayRoute(decodedGeometry);
    } else {
      this.noRouteFound = true;
      this.noRouteMessage = 'No route was found for the given locations.';
    }
  } catch (error) {

    this.noRouteMessage = 'No route was found for the given locations.';

    this.noRouteFound = true;
  } finally {
    this.calculating = false;
  }
},

    formatDrivingTime(duration) {
      const hours = Math.floor(duration / 3600);
      const minutes = Math.floor((duration % 3600) / 60);
      return `${hours}h ${minutes}min`;
    },
  },
};
</script>

<style scoped>

.submit {
  color: white;
}

   .wrapper {
    .navigation{
      width: 100%;
      height: 77px;
      display: flex;
      align-items: center;
      padding-left: 61px;
      padding-top: 12px;

      box-shadow: 0px 4px 8px 0px #00000040;
      img{
        display: flex;
        align-items: center;
      }
    }
      .form {
        padding-left: 29px;
        .title {
          margin-bottom: 20px;
        }
        .label {
          margin-bottom: 9px;
        }
        .guid {
          text-align: right;
          text-decoration: underline;
          text-underline-offset: 5px; 
          cursor: pointer;
        }
        .custom-input{
          padding-top: 3px;
        }
        margin-left: 20px;
        .submit {
          color: white;
          background-color: #E50043;
          width: 90%;
          height: 43px;
          margin: auto;
          display: block;
        }
        .error_message{
          h3{
            margin-top: 30px;
            margin-bottom: 22px;
          }
          p {
            border: 1px solid red;
            width: 90%;
            height: 56px;
            margin: auto;
            display: block;
            display: flex;
            align-items: center;
            padding-left: 15px;
          }
          img{
            margin-right: 5px;
          }
        
        }
        .route_title{
            margin-top: 60px;
          }
        .route_details{
          .content {
            border: 1px solid #9E9E9E;
            height: 97px;
            border-radius: 8px;
            display: block;
            padding: 14px;
            .elem {
              display: flex;
              align-items: center;
            }
            .indicator {
              margin-top: 5px;
            }
            .elem :first-child{
                margin-right: 5px;
              }
          }
        }
      }
    }
</style>


