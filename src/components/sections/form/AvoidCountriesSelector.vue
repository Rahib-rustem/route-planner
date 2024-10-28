  <template>
    <v-col>
      <h3 class="features_title">{{ title }}</h3>
      <v-select
        v-model="selectedCountries"
        :items="items"
        :label="label"
        :multiple="multiple"
        class="custom-select"
        outlined
        append-icon="mdi-chevron-down" 
        hide-details
        dense
      />
    </v-col>
  </template>
  
  <script>
  export default {
    props: {
      items: {
        type: Array,
        required: true,
      },
      title: {
        type: String,
        default: "More features",
      },
      label: {
        type: String,
        default: "Avoid countries",
      },
      multiple: {
        type: Boolean,
        default: true,
      },
      modelValue: {
        type: Array,
        default: () => [],
      },
    },
    data() {
      return {
        selectedCountries: this.modelValue
      };
    },
    watch: {
      modelValue(newValue) {
        this.selectedCountries = [...newValue];
      },
      selectedCountries(newVal) {
      this.$emit("update:modelValue", newVal);
    },
    },
  };
  </script>
  
  <style scoped>
  .features_title{
    margin-bottom: 15px;
  }
  .custom-select {
    margin-bottom: 20px;
  }
  </style>
  