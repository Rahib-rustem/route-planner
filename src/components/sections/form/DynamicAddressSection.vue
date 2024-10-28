
    <!-- 
        @todo: This component has no functionality yet, I did't get what exactly to do, sorry.
            - title: The main title text for the address section.
            - subtitle: The clickable text (like "Add Address") to add a new address line.
            - @click="addSpot": Calls `addSpot` to dynamically add a new address entry.
            - @click="removeSpot": Calls `removeSpot` to dynamically remove created address entry.
        @todo: Implement `addSpot` to manage adding new addresses to the addresses array.
        @todo: Implement `removeSpot(index)` to handle removing specific addresses.
       
        -->


<template>
    <div>
      <v-row class="title">
        <h4 class="label">{{ title }}</h4>
        <h4 class="label addAddressSection" @click="addSpot">{{ subtitle }}</h4>
      </v-row>
      <div v-for="(address, index) in addresses" :key="'address-' + index" class="address-row">
        <v-row>
          <CountrySelector
           
          />
          <AddressInput
          />
        </v-row>
        <span class="remove_address" v-if="index > 0" @click="removeSpot(index)">X</span>
      </div>
    </div>
  </template>
  
  <script>
  import AddressInput from './AddressInput.vue';
  import CountrySelector from './CountrySelector.vue';
  
  export default {
    components: {
      AddressInput,
      CountrySelector,
    },
    props: {
      countries: {
        type: Array,
        required: true,
      },
      title: {
        type: String,
        required: true,
      },
      subtitle: {
        type: String,
        required: true,
      },
    },
    data() {
      return {
        addresses: [{ country: this.countries[0], address: '' }],
      };
    },
    methods: {
  
        }
      };
  </script>
  
  <style scoped>
  .title{
    display: flex;
    justify-content: space-between;
    padding: 0 15px;
    align-items: center;
  }
  .address-row{
    position: relative;
  }
  .remove_address{
    position: absolute;
    right: -21px;
    top: 21px;
  }
   .addAddressSection {
    text-align: right;
    text-decoration: underline;
    text-underline-offset: 5px;
    cursor: pointer;
    margin-bottom: 5px;
  }
  </style>
   