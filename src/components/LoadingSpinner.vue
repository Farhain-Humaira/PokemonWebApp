<template>
  <div class="loader" role="status"></div>
</template>
  
  <script>
  export default {
    name: 'LoadingSpinner',
  };
  </script>
  
  <style scoped>
  .loader {
  border: 5px solid #000000;
  border-radius: 50%;
  border-top: 5px solid #2e4352;
  width: 50px;
  height: 50px;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
  </style>
  