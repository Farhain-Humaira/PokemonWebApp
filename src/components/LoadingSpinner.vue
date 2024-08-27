<template>
  <div class="loader-wrapper">
    <div class="loader" role="status"></div>
  </div>
</template>

<script>
export default {
  name: 'LoadingSpinner',
};
</script>

<style scoped>
.loader-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh; 
}

.loader {
  border: 5px solid #000000;
  border-radius: 50%;
  border-top: 5px solid #138adf;
  width: 25px;
  height: 25px;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style>
