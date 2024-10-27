<template>
    <v-col>
      <h3 class="route_title">Route details</h3>
      <v-row class="route_details">
        <RouteDetailItem
          :imgSrc="imgSrcDistance"
          label="Distance"
          :value="details.distance + ' km'"
        />
        <RouteDetailItem
          :imgSrc="imgSrcTime"
          label="Driving Time"
          :value="details.time"
        />
      </v-row>
    </v-col>
  </template>
  
  <script>
  import RouteDetailItem from './RouteDetailItem.vue';
  export default {
   data() {
    return {
      imgSrcDistance: require('../../../assets/distance.png'),
      imgSrcTime: require('../../../assets/time.png'),

    }
   },
    props: {
      details: Object,
    },
    components: {
      RouteDetailItem,
    },
  };
  </script>
  
  <style scoped>
  .route_title {
    margin-top: 60px;
    margin-bottom: 20px;
  }
  .route_details {
    display: flex;
    justify-content: space-between;
  }
  </style>
  