import { defineStore } from 'pinia';

export const usePokemonStore = defineStore('pokemon', {
  state: () => ({
    pokemonList: [],
    loading: false,
  }),
  actions: {
    async fetchPokemon() {
      this.loading = true;
      try {
        const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=100');
        const data = await response.json();
        const pokemonDetails = await Promise.all(
          data.results.map(pokemon => fetch(pokemon.url).then(res => res.json()))
        );
        this.pokemonList = pokemonDetails;
      } catch (error) {
        console.error('Error fetching Pokémon:', error);
      } finally {
        this.loading = false;
      }
    },
    async updatePokemon(updatedPokemon) {
      const index = this.pokemonList.findIndex(pokemon => pokemon.id === updatedPokemon.id);
      if (index !== -1) {
        this.pokemonList[index] = updatedPokemon;
      } else {
        console.error('Pokémon not found for update:', updatedPokemon);
      }
    },
  },
});
