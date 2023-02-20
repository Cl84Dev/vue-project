<script setup>
import PokemonSearch from "./components/PokemonSearch.vue";
import "./App.css";
</script>

<template>
  <div class="d-flex flex-column align-items-center">
    <img src="../public/Pokémon-Logo.png" alt="Pokemon" class="m-2" />
    <PokemonSearch />
  </div>
</template>

<style>
#app {
  font-family: Roboto, Helvetica, Arial, sans-serif;
}
</style>
