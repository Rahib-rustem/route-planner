<template>
    <v-col>
      <div class="content">
        <div class="elem">
          <img :src="imgSrc" alt="icon" />
          <span>{{ label }}:</span>
        </div>
        <h2 class="indicator">{{ value }}</h2>
      </div>
    </v-col>
  </template>
  
  <script>
  export default {
    props: {
      imgSrc: {
        type: String,
        required: true,
      },
      label: {
        type: String,
        required: true,
      },
      value: {
        type: String,
        required: true,
      },
    },
  };
  </script>
  
  <style scoped>
  .content {
    justify-content: space-between;
    align-items: center;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 5px;
    margin-bottom: 15px;
  }
  .elem {
    display: flex;
    align-items: center;
  }
  .elem img {
    width: 16px;
    height: 16px;
    margin-right: 10px;
  }
  .indicator {
    font-size: 18px;
    color: #333;
  }
  </style>
  