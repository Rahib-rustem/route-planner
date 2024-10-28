<script
src="https://maps.googleapis.com/maps/api/js?key=AIzaSyDcuSoUhAyNDtR-7FzCaX94iGRTK4oV2Ms&libraries=places"
async
defer
></script> 

<template>
  <v-col cols="8 form-controll">
    <h4 class="label guid">{{ subtitle }}</h4>
      <v-text-field
        v-model="localValue"
        :label="label"
        :subtitle="subtitle"
        class="custom-input"
        outlined
        dense
        @input="onInput"
        autocomplete="off"
      />
     
    <v-list v-if="predictions.length" class="autocomplete-dropdown">
      <v-list-item-group>
        <v-list-item
          v-for="(prediction, index) in predictions"
          :key="index"
          @click="selectAddress(prediction)"
        >
          <v-list-item-title>{{ prediction.description }}</v-list-item-title>
        </v-list-item>
      </v-list-item-group>
    </v-list>
  </v-col>
</template>

<template>
  <v-col cols="8 column">
    <h4 class="label guid">{{ subtitle }}</h4>
    <v-text-field
      v-model="localValue"
      :label="label"
      :subtitle="subtitle"
      class="custom-input"
      outlined
      dense
      @input="onInput"
      autocomplete="off"
    />

    <v-list v-if="predictions.length" class="autocomplete-dropdown">
      <v-list-item-group>
        <v-list-item
          v-for="(prediction, index) in predictions"
          :key="index"
          @click="selectAddress(prediction)"
        >
          <v-list-item-title>{{ prediction.description }}</v-list-item-title>
        </v-list-item>
      </v-list-item-group>
    </v-list>
  </v-col>
</template>

<script>
export default {
  props: {
    label: String,
    modelValue: String,
    subtitle: String,
    selectedCountry: String,
  },
  data() {
    return {
      localValue: this.modelValue,
      predictions: [],
    };
  },
  watch: {
    localValue(newValue) {
      this.$emit('update:modelValue', newValue);
      this.fetchPredictions(newValue);
    },
    modelValue(newValue) {
      this.localValue = newValue;
    },
  },
  methods: {
    onInput(value) {
      this.localValue = value;
      this.fetchPredictions(value);
    },
    async fetchPredictions(input) {
      if (!input) {
        this.predictions = [];
        return;
      }

      const service = new google.maps.places.AutocompleteService();
      service.getPlacePredictions(
        { input, componentRestrictions: { country: this.selectedCountry } },
        (predictions, status) => {
          if (status === google.maps.places.PlacesServiceStatus.OK) {
            this.predictions = predictions;
          } else {
            this.predictions = [];
          }
        }
      );
    },
    selectAddress(prediction) {
      this.localValue = prediction.description;
      this.predictions = [];

      const service = new google.maps.places.PlacesService(document.createElement('div'));
      service.getDetails({ placeId: prediction.place_id }, (place, status) => {
        if (status === google.maps.places.PlacesServiceStatus.OK) {
          this.$emit('placeSelected', {
            address: place.formatted_address,
            lat: place.geometry.location.lat(),
            lng: place.geometry.location.lng(),
          });
        }
      });
      this.$emit('update:modelValue', this.localValue);
    },
  },
};
</script>

  <style scoped>
  .guid {
    text-align: right;
    text-decoration: underline;
    text-underline-offset: 5px;
    cursor: pointer;
    margin-bottom: 5px;
  }
  .column {
    padding-bottom: 0;
  }
  .custom-input {
    padding-top: 3px;
   
  }

.autocomplete-dropdown {
  background-color: white;
  box-shadow: rgba(0, 0, 0, 0.19) 0px 10px 20px, rgba(0, 0, 0, 0.23) 0px 6px 6px!important;
  border-radius: 4px;
  z-index: 1000;
  position: absolute;
  max-height: 300px;
  overflow-y: auto;
}

.v-list-item {
  transition: background-color 0.2s;
}

.v-list-item:hover {
  background-color: #f5f5f5;
}
  </style>
  