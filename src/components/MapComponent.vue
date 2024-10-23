

<script
src="https://maps.googleapis.com/maps/api/js?key=AIzaSyDcuSoUhAyNDtR-7FzCaX94iGRTK4oV2Ms&libraries=places"
async
defer
></script> 

<template>
  <div ref="map" class="map-container" style="width: 100%; height: 100vh;"></div>
</template>

<script>
export default {
  name: 'MapComponent',
  data() {
    return {
      map: null,
      directionsService: null,
      directionsRenderer: null,
      routePath: null,
    };
  },
  mounted() {
    this.initMap();
  },
  methods: {
    initMap() {
      this.map = new google.maps.Map(this.$refs.map, {
        center: { lat: 52.5200, lng: 13.4050 },
        zoom: 6,
      });

      this.directionsService = new google.maps.DirectionsService();
      this.directionsRenderer = new google.maps.DirectionsRenderer({
        preserveViewport: true,
        suppressMarkers: true,
        map: this.map,
        polylineOptions: {
          strokeColor: 'red',
          strokeOpacity: 1.0,
          strokeWeight: 4,
        },
      });
    },
    displayRoute(routeGeometry) {

      const coordinates = routeGeometry.map(coord => ({
          lat: parseFloat(coord[0].toFixed(6)),
          lng: parseFloat(coord[1].toFixed(6)),
        }));

      const routePath = new google.maps.Polyline({
        path: coordinates,
        geodesic: true,
        strokeColor: '#FF0000',
        strokeOpacity: 1.0,
        strokeWeight: 4,
      });

      if (this.routePath) {
        this.routePath.setMap(null);
      }

      routePath.setMap(this.map);
      this.routePath = routePath;
        
      const bounds = new google.maps.LatLngBounds();
      coordinates.forEach(coord => bounds.extend(coord));
      this.map.fitBounds(bounds);
    },
  },
};
</script>

<style scoped>
</style>



