   <template>
    <v-col cols="4 column">
      <h3 class="label">{{ title }}</h3>
      <v-select
          v-model="localValue"
          :items="items"
          :label="label"
          class="custom-select"
          outlined
          dense
      />
    </v-col>
  </template>
  
  <script>
  export default {
    props: {
      title: String,
      label: String,
      items: Array,
      value: String,
    },
    data() {
      return {
        localValue: this.value,
      };
    },
    watch: {
      localValue(newValue) {
        this.$emit('input', newValue);
      },
      value(newValue) {
        this.localValue = newValue;
      },
      items: {
      immediate: true,
      handler(newItems) {
        if (newItems && newItems.length > 0) {
          this.localValue = newItems[0];
        }
      },
    },
    },

  };
  </script>
  
  <style scoped>
  .column {
    padding-bottom: 0;
  }
  .label {
    margin-bottom: 5px;
  }
  .custom-select {
    width: 100%;
  }
  </style>
  