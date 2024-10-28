

   <script
   src="https://maps.googleapis.com/maps/api/js?key=AIzaSyDcuSoUhAyNDtR-7FzCaX94iGRTK4oV2Ms&libraries=places"
   async
   defer
   ></script> 

   <template>
    <v-form ref="routeForm" v-model="localValid" lazy-validation class="form">
      <h2 class="title">{{ title }}</h2>
      <v-row>
        <CountrySelector
          title="Loading"
          label="Country"
          v-model="localLoadingCountry"
          :items="countries"
          required
        />
        <AddressInput
        ref="loadingAddressInput"
        label="Address"
        subtitle="Add loading spot"
        v-model="localLoadingAddress"
        :selected-country="localLoadingCountry"
        required
        @placeSelected="onLoadingPlaceSelected"
        />
      </v-row>
      <v-row>
        <CountrySelector
          title="Unloading"
          label="Country"
          v-model="localUnloadingCountry"
          :items="countries"
          required
        />
        <AddressInput
            ref="unloadingAddressInput"
            label="Address"
            subtitle="Add unloading spot"
            v-model="localUnloadingAddress"
            :selected-country="localUnloadingCountry"
            required
            @placeSelected="onUnloadingPlaceSelected"
        />
      </v-row>
      <v-row>
        <AvoidCountriesSelector
          v-model="localAvoidCountries"
          :items="countries"
          @update:modelValue="onAvoidCountriesChange"
        />
      </v-row>
      <v-row>
        <SubmitButton
           :disabled="isCalculateButtonDisabled"
          :calculating="localCalculating"
          @submit="submit"
        />
      </v-row>
      <v-row v-if="routeDetails && routeDetails.distance || noRouteFound" class="seperator"/>
      <v-row v-if="routeDetails && routeDetails.distance">
        <RouteDetails :details="routeDetails" />
      </v-row>
    </v-form>
  </template>
  
  <script>
  import axios from 'axios';
  import polyline from 'polyline';
  import CountrySelector from './CountrySelector.vue';
  import AddressInput from './AddressInput.vue';
  import AvoidCountriesSelector from './AvoidCountriesSelector.vue';
  import SubmitButton from './SubmitButton.vue';
  import RouteDetails from '../indigator/RouteDetails.vue';
  
  export default {
    components: {
      CountrySelector,
      AddressInput,
      AvoidCountriesSelector,
      SubmitButton,
      RouteDetails,
    },
    props: {
      title: String,
      countries: Array,
      avoidCountries: Array,
      valid: Boolean,
      calculating: Boolean,
    },
    data() {
      return {
        localLoadingCountry: 'DE',
        localLoadingAddress: '',
        localUnloadingCountry: 'DE',
        localUnloadingAddress: '',
        localAvoidCountries: this.avoidCountries,
        localValid: this.valid,
        loadingAddressLat: null,
        loadingAddressLng: null,
        unloadingAddressLat: null,
        unloadingAddressLng: null,
        noRouteFound: false,
        noRouteMessage: '',
        routeDetails: null,
        routeGeometry: [],
        localCalculating: this.calculating,
      };
    },
  
    watch: {
      localLoadingCountry(newVal) {
        this.$emit('update:loadingCountry', newVal);
      },
      localLoadingAddress(newVal) {
        this.$emit('update:loadingAddress', newVal);
      },
      localUnloadingCountry(newVal) {
        this.$emit('update:unloadingCountry', newVal);
      },
      localUnloadingAddress(newVal) {
        this.$emit('update:unloadingAddress', newVal);
      },
      localAvoidCountries(newVal) {
        this.$emit('update:avoidCountries', newVal);
      },
    },
  
    computed: {
      isCalculateButtonDisabled() {
        return !(
          this.localLoadingCountry &&
          this.localLoadingAddress &&
          this.localUnloadingCountry &&
          this.localUnloadingAddress
        );
      },
    },
  
    methods: {
      async onAvoidCountriesChange(selectedCountries) {
        this.localAvoidCountries = selectedCountries;
      },

      onLoadingPlaceSelected(place) {
      this.localLoadingAddress = place.address;
      this.loadingAddressLat = place.lat;
      this.loadingAddressLng = place.lng;
    },
    onUnloadingPlaceSelected(place) {
      this.localUnloadingAddress = place.address;
      this.unloadingAddressLat = place.lat;
      this.unloadingAddressLng = place.lng;
    },
      async calculateRoute() {
        this.localCalculating = true;
        this.$emit('update:calculating', true);
        this.noRouteFound = false;
        this.noRouteMessage = '';
        this.routeDetails = null;
        const payload = {
          coordinates: [
            [this.loadingAddressLng, this.loadingAddressLat],
            [this.unloadingAddressLng, this.unloadingAddressLat],
          ],
          options: {
            avoid_countries: this.localAvoidCountries,
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
            this.$emit('routeCalculated', this.routeDetails, decodedGeometry);
          } else {
            this.noRouteFound = true;
            this.noRouteMessage = 'No route was found for the given locations.';
            this.$emit('routeError', this.noRouteMessage);
          }
        } catch (error) {
          this.noRouteFound = true;
          this.noRouteMessage = error.response?.data?.message || 'No route was found for the given locations.';
          this.$emit('routeError', this.noRouteMessage);
        } finally {
          this.localCalculating = false;
          this.$emit('update:calculating', false);
        }
      },
  
      formatDrivingTime(duration) {
        const hours = Math.floor(duration / 3600);
        const minutes = Math.floor((duration % 3600) / 60);
        return `${hours}h ${minutes}min`;
      },
  
      submit() {
        this.calculateRoute();
        this.$emit('submit');
      },
    },
  };
  </script>

  
  <style scoped>
  .form {
    padding-left: 13px;
    margin-right: 13px;
    margin-left: 20px;
    .seperator {
      margin-top: 80px;
      border-bottom: 1px solid #9E9E9E
    }
  }
  .title {
    margin-top: 50px;
    margin-bottom: 20px;
    
  }
  </style>
  