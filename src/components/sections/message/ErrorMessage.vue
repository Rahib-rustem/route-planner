  <template>
    <section class="error_message">
      <h3 class="title">{{ title }}</h3>
      <div v-if="message">
        <figure class="content">
          <img src="../../../assets/warning.png" alt="warning" width="18px" />
          {{ message }}
        </figure>
      </div>
    </section>
  </template>
  
  <script>
  export default {
    props: {
      title: {
        type: String,
        required: true
      },
      message: {
        type: String,
        required: true,
      },
    },
  };
  </script>
  
  <style scoped>
  .error_message {
  
    width: 90%;
    margin: auto;
    .title {
      margin-top: 60px!important;
      margin-bottom: 30px;
    }
  }
  .error_message .content {
    border: 1px solid red;
    height: 56px;
    display: flex;
    align-items: center;
    padding-left: 15px;
    margin: 0;
    display: flex;
    align-items: center;
    img{
      margin-right: 10px;
    }
  }
  </style>
  