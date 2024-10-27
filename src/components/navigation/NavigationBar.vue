<template>
    <header class="navigation">
      <img :src="logoSrc" height="40px" alt="Company Logo" />
    </header>
  </template>
  
  <script>
  export default {
    props: {
      logoSrc: {
        type: String,
        required: true,
      },
    },
  };
  </script>
  
  <style scoped>
  .navigation {
    width: 100%;
    height: 77px;
    display: flex;
    align-items: center;
    padding: 12px 61px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.25);
  }
  </style>
  