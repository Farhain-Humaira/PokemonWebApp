<template>
  <div class="container">
    <h1 class="text-center my-4"></h1>
    <div class="search-container mb-4">
      <input type="text" v-model="searchQuery" placeholder="Search Pokémon..." class="form-control search-input"/>
      <button class="btn btn-primary search-button">
        <i class="bi bi-search"></i> Search
      </button>
    </div>
    <div v-if="loading" class="text-center">
    <Loader  />
    </div>
    <div v-else class="row">
      <div v-for="pokemon in filteredPokemon" :key="pokemon.id" class="col-md-3 mb-4">
        <div :class="['card', `bg-${pokemon.types[0].type.name}`]">
          <img :src="pokemon.sprites.front_default" class="card-img-top" :alt="pokemon.name">
          <div class="card-body">
            <h5 class="card-title">{{ pokemon.name }}</h5>
            <button @click="showDetails(pokemon)" class="btn btn-info">View</button>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal -->
    <div v-if="selectedPokemon" :class="['modal fade', `modal-bg-${selectedPokemon.types[0].type.name}`]" id="pokemonModal" tabindex="-1" aria-labelledby="pokemonModalLabel" aria-hidden="true">
      <div class="modal-dialog modal-lg">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="pokemonModalLabel">{{ selectedPokemon.name }}</h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <div class="d-flex">
              <div class="pokemon-image-container">
                <img :src="selectedPokemon.sprites.front_default" class="img-fluid" :alt="selectedPokemon.name">
                <p class="pokemon-name">{{ selectedPokemon.name }}</p>
              </div>
              <div class="pokemon-details ms-3">
                <p class="detail-item"><strong>Type:</strong> <span>{{ selectedPokemon.types[0].type.name }}</span></p>
                <p class="detail-item"><strong>Base Experience:</strong> <span>{{ selectedPokemon.base_experience }}</span></p>
                <p class="detail-item"><strong>Height:</strong> <span>{{ selectedPokemon.height }} decimetres</span></p>
                <p class="detail-item"><strong>Weight:</strong> <span>{{ selectedPokemon.weight }} hectograms</span></p>
                <p class="detail-item"><strong>Abilities:</strong></p>
                <ul class="abilities-list">
                  <li v-for="ability in selectedPokemon.abilities" :key="ability.ability.name" class="ability-item">
                    {{ ability.ability.name }}
                  </li>
                </ul>
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
            <button type="button" class="btn btn-primary" @click="openEditModal">Edit</button>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal for Editing Pokémon -->
    <div v-if="editPokemon" class="modal fade" id="editPokemonModal" tabindex="-1" aria-labelledby="editPokemonModalLabel" aria-hidden="true">
      <div class="modal-dialog modal-lg">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="editPokemonModalLabel">Edit {{ editPokemon.name }}</h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <!-- Form for editing Pokémon information -->
            <form @submit.prevent="updatePokemon">
              <div class="mb-3">
                <label for="pokemonName" class="form-label">Name</label>
                <input type="text" id="pokemonName" v-model="editPokemon.name" class="form-control" required />
              </div>
              <div class="mb-3">
                <label for="pokemonHeight" class="form-label">Height (decimetres)</label>
                <input type="number" id="pokemonHeight" v-model="editPokemon.height" class="form-control" required />
              </div>
              <div class="mb-3">
                <label for="pokemonWeight" class="form-label">Weight (hectograms)</label>
                <input type="number" id="pokemonWeight" v-model="editPokemon.weight" class="form-control" required />
              </div>
              <div class="mb-3">
                <label for="pokemonBaseExperience" class="form-label">Base Experience</label>
                <input type="number" id="pokemonBaseExperience" v-model="editPokemon.base_experience" class="form-control" required />
              </div>
              <!-- Add more fields as needed -->
              <button type="submit" class="btn btn-primary">Save</button>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

  
<script>
import { ref, computed, onMounted } from 'vue';
import { usePokemonStore } from '../stores/pokemonStore';
import Loader from './LoadingSpinner.vue';
import 'bootstrap/dist/js/bootstrap.bundle.min.js';

export default {
  components: {
    Loader,
  },
  setup() {
    const pokemonStore = usePokemonStore();
    const searchQuery = ref('');
    const selectedPokemon = ref(null);
    const editPokemon = ref(null);

    const fetchPokemon = async () => {
      await pokemonStore.fetchPokemon();
    };

    onMounted(fetchPokemon);

    const filteredPokemon = computed(() => {
      return pokemonStore.pokemonList.filter(pokemon =>
        pokemon.name.toLowerCase().includes(searchQuery.value.toLowerCase())
      );
    });

    const showDetails = async (pokemon) => {
      selectedPokemon.value = pokemon;
      editPokemon.value = { ...pokemon }; // Prepare for editing
      const { Modal } = await import('bootstrap/dist/js/bootstrap.bundle.min.js');
      const modalElement = document.getElementById('pokemonModal');
      const modal = new Modal(modalElement);
      modal.show();
    };

    const openEditModal = async () => {
      const { Modal } = await import('bootstrap/dist/js/bootstrap.bundle.min.js');
      const modalElement = document.getElementById('editPokemonModal');
      const modal = new Modal(modalElement);
      modal.show();
    };

    const updatePokemon = async () => {
      try {
        await pokemonStore.updatePokemon(editPokemon.value);
        Object.assign(selectedPokemon.value, editPokemon.value);

        const { Modal } = await import('bootstrap/dist/js/bootstrap.bundle.min.js');
        const modalElement = document.getElementById('editPokemonModal');
        const modal = Modal.getInstance(modalElement);
        modal.hide();  
      } catch (error) {
        console.error('Error updating Pokémon:', error);
      }
    };


    return {
      searchQuery,
      filteredPokemon,
      loading: pokemonStore.loading,
      selectedPokemon,
      editPokemon,
      showDetails,
      openEditModal,
      updatePokemon,
    };
  },
};

</script>

<style scoped>
.modal-dialog.modal-lg .edit {
  max-width: 500px; 
}

.modal-content {
  border-radius: 0.5rem;
  box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.2);
}

.modal-header {
  background-color: #f8f9fa;
  border-bottom: 1px solid #dee2e6;
  padding: 1rem 1.5rem;
}

.modal-title {
  font-size: 1.25rem;
  font-weight: bold;
  color: #343a40;
}

.modal-body {
  padding: 1rem 1.5rem;
}

.form-label {
  display: block;
  text-align: left; 
  font-weight: bold;
  color: #495057;
}

.form-control {
  border-radius: 0.25rem;
  border-color: #ced4da;
  box-shadow: none;
}

.form-control:focus {
  border-color: #80bdff;
  box-shadow: 0 0 0 0.2rem rgba(38, 143, 255, 0.25);
}

@media (max-width: 576px) {
  .modal-dialog {
    max-width: 90%;
  }
}
</style>
