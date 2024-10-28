

<script
src="https://maps.googleapis.com/maps/api/js?key=AIzaSyDcuSoUhAyNDtR-7FzCaX94iGRTK4oV2Ms&libraries=places"
async
defer
></script> 

<template>
  <div ref="map" class="map-container"></div>
</template>

<script>
export default {
  props: {
    routeGeometry: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      map: null,
      routePath: null,
      startMarker: null,
      endMarker: null,
    };
  },
  watch: {
    routeGeometry(newVal) {
      if (newVal && newVal.length) {
        this.displayRoute(newVal); 
      }
    },
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

      google.maps.event.addListenerOnce(this.map, 'idle', () => {
        google.maps.event.trigger(this.map, 'resize');
      });
    },
    displayRoute(routeGeometry) {
      const coordinates = routeGeometry.map(coord => ({
        lat: parseFloat(coord[0].toFixed(6)),
        lng: parseFloat(coord[1].toFixed(6)),
      }));

      if (this.routePath) {
        this.routePath.setMap(null);
      }

      this.routePath = new google.maps.Polyline({
        path: coordinates,
        geodesic: true,
        strokeColor: '#7087FF',
        strokeOpacity: 1.0,
        strokeWeight: 6,
      });

      this.routePath.setMap(this.map);

      const bounds = new google.maps.LatLngBounds();
      coordinates.forEach(coord => bounds.extend(coord));
      this.map.fitBounds(bounds);

      if (this.startMarker) {
        this.startMarker.setMap(null);
      }
      this.startMarker = new google.maps.Marker({
        position: coordinates[0],
        map: this.map,
        title: 'Start',
        icon: {
          url: require('../../assets/start.png'),
        },
        scaledSize: new google.maps.Size(24, 24)
      });

      if (this.endMarker) {
        this.endMarker.setMap(null);
      }
      this.endMarker = new google.maps.Marker({
        position: coordinates[coordinates.length - 1],
        map: this.map,
        title: 'End',
        icon: {
          url: require('../../assets/end.png'),
        },
      });
    },
  },
};
</script>

<style scoped>
  .map-container {
    width: 100%; 
    display: flex;
    flex-direction: column;
    height: 100%;
    box-sizing: border-box;

  }
</style>
