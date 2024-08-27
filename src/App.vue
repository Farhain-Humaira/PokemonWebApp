<template>
    <section class="welcome-section">
      <img alt="Vue logo" src="./assets/logo.png">
      <h3>Welcome, Trainer!</h3>
      <h6>You’ve just entered the Pokémon world! <br>Get ready to catch, explore, and discover amazing Pokémon.</h6>
    </section>
  <div id="app">
    <PokemonList />
  </div>
</template>

<script>
  import PokemonList from './components/PokemonList.vue';

export default {
  components: {
    PokemonList
  }
};
</script>

<style>
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
section {
  color: white;
  background-color: #2c3e50;
  padding: 10px;
}
</style>
