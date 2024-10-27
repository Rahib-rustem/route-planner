   <template>
    <v-col cols="8">
      <h4 class="label guid">{{ subtitle }}</h4>
      <v-text-field
        v-model="localValue"
        :label="label"
        :subtitle="subtitle"
        class="custom-input"
        outlined
        dense
      />
    </v-col>
  </template>
  
  <script>
  export default {
    props: {
      label: String,
      modelValue: String,
      subtitle: String
    },
    data() {
      return {
        localValue: this.modelValue,
      };
    },
    watch: {
      localValue(newValue) {
        this.$emit('update:modelValue', newValue);
      },
      modelValue(newValue) {
        this.localValue = newValue;
      },
    },
    computed: {},
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
  .custom-input {
    padding-top: 3px;
   
  }
  </style>
  