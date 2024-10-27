<template>
    <v-col>
      <v-btn :disabled="disabled" @click="submit" class="submit">
        {{ calculating ? 'Calculating...' : 'Calculate Route' }}
      </v-btn>
    </v-col>
  </template>
  
  <script>
  export default {
    props: {
      disabled: Boolean,
      calculating: Boolean,
      
    },
    methods: {
      submit() {
        this.$emit('submit');
      },
    },
  };
  </script>
  
  <style scoped>
  .submit {
    color: white;
    background-color: #e50043!important;
    width: 100%;
    height: 43px!important;
    margin: auto;
    display: block;
  }
  </style>
  