

<script
src="https://maps.googleapis.com/maps/api/js?key=AIzaSyDcuSoUhAyNDtR-7FzCaX94iGRTK4oV2Ms&libraries=places"
async
defer
></script> 

<template>
  <v-app class="wrapper">
    <v-row>
      <NavigationBar :logoSrc="logoSrc" />
    </v-row>
    <v-row>
      <v-col cols="4">
        <FormSection
          title="Route configuration"
          :countries="countries"
          :loadingCountry.sync="loadingCountry"
          :unloadingCountry.sync="unloadingCountry"
          :loadingAddress.sync="loadingAddress"
          :unloadingAddress.sync="unloadingAddress"
          :avoidCountries.sync="avoidCountries"
          :valid="valid"
          :calculating="calculating"
          @routeCalculated="handleRouteCalculated"
          @routeError="displayError"
        />
        <ErrorMessage title="Route details" v-if="noRouteFound" :message="noRouteMessage" />
      </v-col>
      <v-col cols="8">
        <MapComponent ref="mapComponent" :routeGeometry="routeGeometry" />
      </v-col>
    </v-row>
  </v-app>
</template>

<script>
import NavigationBar from './components/navigation/NavigationBar';
import FormSection from './components/sections/form/FormSection';
import ErrorMessage from './components/sections/message/ErrorMessage';
import MapComponent from './components/map/MapComponent'


export default {
  components: {
    NavigationBar,
    FormSection,
    ErrorMessage,
    MapComponent
  },
  data() {
    return {
      logoSrc: require('./assets/logo.png'),
      countries: ['DE', 'CH', 'AT', 'NL', 'BE', 'FR', 'ES', 'PL'], 
      loadingCountry: '',
      loadingAddress: '',
      unloadingCountry: '',
      unloadingAddress: '',
      avoidCountries: [],
      valid: false,
      calculating: false,
      routeDetails: null,
      noRouteFound: false,
      noRouteMessage: '',
      routeGeometry: null,
    };
  },
  methods: {
    handleRouteCalculated(routeDetails, decodedGeometry) {
      this.noRouteFound = false;
      this.routeDetails = routeDetails;
      this.routeGeometry = decodedGeometry;
    },

    calculateRoute() {
      this.calculating = true;
    },
    displayError(message) {
      this.$refs.mapComponent.initMap();
      this.noRouteFound = true;
      this.noRouteMessage = message;
    },
  },
};
</script>


<style scoped>
.wrapper {
  padding: 20px;
}
</style>
