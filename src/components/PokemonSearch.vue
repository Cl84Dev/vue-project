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
      // poke_name: "",
      // poke_img: "",
      // hp: "",
      // attack: "",
      // defense: "",
      // special_attack: "",
      // special_defense: "",
      // speed: "",
      show_error: false,
      show_stats: false,
      poke_data_chain: null,
    };
  },
  methods: {
    // async sendName() {
    //   try {
    //     const response = await axios.get(
    //       `https://pokeapi.co/api/v2/pokemon/${this.input_search.toLowerCase()}`
    //     );
    //     this.poke_name = response.data.name;
    //     this.poke_img = response.data.sprites.other.dream_world.front_default;
    //     this.hp = response.data.stats[0].base_stat;
    //     this.attack = response.data.stats[1].base_stat;
    //     this.defense = response.data.stats[2].base_stat;
    //     this.special_attack = response.data.stats[3].base_stat;
    //     this.special_defense = response.data.stats[4].base_stat;
    //     this.speed = response.data.stats[4].base_stat;
    //     this.show_error = false;
    //     this.show_stats = false;
    //   } catch (error) {
    //     console.error(error);
    //     this.poke_name = null;
    //     this.show_error = true;
    //   }
    // },
    showStats() {
      this.show_stats = !this.show_stats;
    },
    async evolutionChain() {
      try {
        const response = await axios.get(
          `https://pokeapi.co/api/v2/pokemon/${this.input_search.toLowerCase()}`
        );
        const species = await axios.get(response.data.species.url);
        const evolutionChain = await axios.get(
          species.data.evolution_chain.url
        );
        const first = evolutionChain.data.chain.species.name;

        this.poke_data_chain = [];
        this.show_error = false;
        this.show_stats = false;

        if (await first) {
          const response = await axios.get(
            `https://pokeapi.co/api/v2/pokemon/${first}`
          );
          this.poke_data_chain.push(response);
        }

        const second = first
          ? evolutionChain.data.chain.evolves_to[0].species.name
          : null;

        if (await second) {
          const response = await axios.get(
            `https://pokeapi.co/api/v2/pokemon/${second}`
          );
          this.poke_data_chain.push(response);
        }

        const third = second
          ? evolutionChain.data.chain.evolves_to[0].evolves_to[0].species.name
          : null;

        if (await third) {
          const response = await axios.get(
            `https://pokeapi.co/api/v2/pokemon/${third}`
          );
          this.poke_data_chain.push(response);
        }

        console.log(first, second, third, this.poke_data_chain);
      } catch (error) {
        this.poke_data_chain = null;
        this.show_error = true;
      }
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
      <MDBBtn color="primary" v-on:click="evolutionChain">
        <MDBIcon icon="search" />
      </MDBBtn>
    </MDBInput>
    <!-- <div v-if="show_error && !poke_name">Pokemon not found</div> -->
    <!-- <div v-if="!show_error && poke_name" class="card p-3">
      <MDBCard>
        <a v-mdb-ripple="{ color: 'light' }">
          <MDBCardImg :src="poke_img" top alt="..." class="img" />
        </a>
        <MDBCardBody class="d-flex flex-column align-items-center">
          <MDBCardTitle>{{ poke_name.toUpperCase() }}</MDBCardTitle>
          <MDBBtn
            tag="a"
            href="#!"
            color="primary"
            class="my-3"
            v-on:click="showStats"
            >{{ !show_stats ? "Show Stats" : "Hide Stats" }}</MDBBtn
          >
        </MDBCardBody>
      </MDBCard>
      <MDBCard
        style="width: 100%"
        class="align-self-center my-3"
        v-if="show_stats"
      >
        <MDBListGroup flush>
          <MDBListGroupItem
            ><span class="fw-bold">HP: </span>{{ hp }}</MDBListGroupItem
          >
          <MDBListGroupItem
            ><span class="fw-bold">Attack: </span>{{ attack }}</MDBListGroupItem
          >
          <MDBListGroupItem
            ><span class="fw-bold">Defense: </span
            >{{ defense }}</MDBListGroupItem
          >
          <MDBListGroupItem
            ><span class="fw-bold">Special Attack: </span
            >{{ special_attack }}</MDBListGroupItem
          >
          <MDBListGroupItem
            ><span class="fw-bold">Special Defense: </span
            >{{ special_defense }}</MDBListGroupItem
          >
          <MDBListGroupItem
            ><span class="fw-bold">Speed: </span>{{ speed }}</MDBListGroupItem
          >
        </MDBListGroup>
      </MDBCard>
    </div> -->
    <div v-if="show_error && !poke_data_chain">Pokemon not found</div>
    <div
      v-if="!show_error && poke_data_chain"
      class="card p-3"
      v-for="poke_data in poke_data_chain"
      :key="poke_data.data"
    >
      <MDBCard>
        <a v-mdb-ripple="{ color: 'light' }">
          <MDBCardImg
            :src="poke_data.data.sprites.other.dream_world.front_default"
            top
            alt="..."
            class="img"
          />
        </a>
        <MDBCardBody class="d-flex flex-column align-items-center">
          <MDBCardTitle>{{ poke_data.data.name.toUpperCase() }}</MDBCardTitle>
          <MDBBtn
            tag="a"
            href="#!"
            color="primary"
            class="my-3"
            v-on:click="showStats"
            >{{ !show_stats ? "Show Stats" : "Hide Stats" }}</MDBBtn
          >
        </MDBCardBody>
      </MDBCard>
      <MDBCard
        style="width: 100%"
        class="align-self-center my-3"
        v-if="show_stats"
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
