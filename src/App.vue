<script setup>
import PokemonSearch from "./components/PokemonSearch.vue";
import "./App.css";
</script>

<template>
  <div class="d-flex flex-column align-items-center">
    <img
      src="https://imagensemoldes.com.br/wp-content/uploads/2020/04/Pok%C3%A9mon-Logo-PNG.png"
      alt="Pokemon"
    />
  </div>
  <PokemonSearch />
</template>

<style>
#app {
  font-family: Roboto, Helvetica, Arial, sans-serif;
}
</style>
