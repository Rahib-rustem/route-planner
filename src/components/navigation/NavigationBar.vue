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
    position: relative;
    width: 100%;
    z-index: 99;
    height: 77px;
    display: flex;
    align-items: center;
    padding: 12px 61px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.25);
    margin-bottom: 13px;
  }
  </style>
  