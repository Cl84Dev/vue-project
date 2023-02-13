<script>
import {
  MDBInput,
  MDBIcon,
  MDBCard,
  MDBCardBody,
  MDBCardTitle,
  MDBCardImg,
  MDBBtn,
  mdbRipple,
  MDBListGroup,
  MDBListGroupItem,
} from "mdb-vue-ui-kit";

import axios from "axios";
import "./PokemonSearch.css";

export default {
  components: {
    MDBInput,
    MDBBtn,
    MDBIcon,
    MDBCard,
    MDBCardBody,
    MDBCardTitle,
    MDBCardImg,
    MDBListGroup,
    MDBListGroupItem,
  },
  directives: {
    mdbRipple,
  },
  data() {
    return {
      input_search: "",
      show_error: false,
      poke_data_chain: null,
    };
  },
  methods: {
    async searchPokemon() {
      try {
        this.poke_data_chain = [];
        this.show_error = false;

        const response = await axios.get(
          `https://pokeapi.co/api/v2/pokemon/${this.input_search.toLowerCase()}`
        );
        const species = await axios.get(response.data.species.url);
        const evolutionChain = await axios.get(
          species.data.evolution_chain.url
        );

        const baby = evolutionChain.data.chain.species.name;

        if (baby) {
          const response = await axios.get(
            `https://pokeapi.co/api/v2/pokemon/${baby}`
          );
          this.poke_data_chain.push({ show: false, data: response.data });
        }

        const evolution = evolutionChain.data.chain.evolves_to;

        if (evolution) {
          evolution.map(async (item) => {
            const response = await axios.get(
              `https://pokeapi.co/api/v2/pokemon/${item.species.name}`
            );
            this.poke_data_chain.push({ show: false, data: response.data });
          });
        }

        const secondEvolution = evolution.map(
          (item, index) => item.evolves_to[index]
        );

        if (secondEvolution) {
          secondEvolution.map(async (item) => {
            if (item) {
              const response = await axios.get(
                `https://pokeapi.co/api/v2/pokemon/${item.species.name}`
              );
              this.poke_data_chain.push({ show: false, data: response.data });
            }
          });
        }
      } catch (error) {
        console.error(error);
        this.poke_data_chain = null;
        this.show_error = true;
      }
    },
    showStats(id) {
      this.poke_data_chain.forEach((item) => {
        if (item.data.id === id) {
          item.show = !item.show;
        }
      });
    },
  },
};
</script>

<template>
  <main class="mx-auto my-5 d-flex flex-column align-items-center">
    <MDBInput
      v-model="input_search"
      inputGroup
      :formOutline="false"
      wrapperClass="mb-3"
      placeholder="Enter Pokemon name"
      aria-label="Search"
      aria-describedby="button-addon2"
    >
      <MDBBtn color="primary" v-on:click="searchPokemon">
        <MDBIcon icon="search" />
      </MDBBtn>
    </MDBInput>
    <div v-if="show_error && !poke_data_chain">Pokemon not found</div>
    <div
      v-if="!show_error && poke_data_chain"
      class="card p-3 my-3 border"
      v-for="poke_data in poke_data_chain"
      :key="poke_data.data"
    >
      <MDBCard>
        <a v-mdb-ripple="{ color: 'light' }">
          <MDBCardImg
            :src="poke_data.data.sprites.other.dream_world.front_default"
            top
            :alt="`${poke_data.data.name.toUpperCase()}`"
          />
        </a>
        <MDBCardBody class="d-flex flex-column align-items-center">
          <MDBCardTitle>{{ poke_data.data.name.toUpperCase() }}</MDBCardTitle>
          <MDBBtn
            tag="a"
            href="#!"
            color="primary"
            class="my-3"
            v-on:click="showStats(poke_data.data.id)"
            >{{ !poke_data.show ? "Show Stats" : "Hide Stats" }}</MDBBtn
          >
        </MDBCardBody>
      </MDBCard>
      <MDBCard
        style="width: 100%"
        class="align-self-center my-3"
        v-if="poke_data.show"
      >
        <MDBListGroup flush>
          <MDBListGroupItem
            ><span class="fw-bold">HP: </span
            >{{ poke_data.data.stats[0].base_stat }}</MDBListGroupItem
          >
          <MDBListGroupItem
            ><span class="fw-bold">Attack: </span
            >{{ poke_data.data.stats[1].base_stat }}</MDBListGroupItem
          >
          <MDBListGroupItem
            ><span class="fw-bold">Defense: </span
            >{{ poke_data.data.stats[2].base_stat }}</MDBListGroupItem
          >
          <MDBListGroupItem
            ><span class="fw-bold">Special Attack: </span
            >{{ poke_data.data.stats[3].base_stat }}</MDBListGroupItem
          >
          <MDBListGroupItem
            ><span class="fw-bold">Special Defense: </span
            >{{ poke_data.data.stats[4].base_stat }}</MDBListGroupItem
          >
          <MDBListGroupItem
            ><span class="fw-bold">Speed: </span
            >{{ poke_data.data.stats[5].base_stat }}</MDBListGroupItem
          >
        </MDBListGroup>
      </MDBCard>
    </div>
  </main>
</template>
