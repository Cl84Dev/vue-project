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
      poke_name: "",
      poke_img: "",
      show_error: false,
      show_stats: false,
    };
  },
  methods: {
    async sendName() {
      try {
        const response = await axios.get(
          `https://pokeapi.co/api/v2/pokemon/${this.input_search.toLowerCase()}`
        );
        this.poke_name = response.data.name;
        this.poke_img = response.data.sprites.other.dream_world.front_default;
        this.show_error = false;
        this.show_stats = false;
      } catch (error) {
        console.error(error);
        this.poke_name = null;
        this.show_error = true;
      }
    },
    showStats() {
      this.show_stats = !this.show_stats;
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
      <MDBBtn color="primary" v-on:click="sendName">
        <MDBIcon icon="search" />
      </MDBBtn>
    </MDBInput>
    <div v-if="show_error && !poke_name">Pokemon not found</div>
    <div v-if="!show_error && poke_name" class="card p-3">
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
          <MDBListGroupItem>Cras justo odio</MDBListGroupItem>
          <MDBListGroupItem>Dapibus ac facilisis in</MDBListGroupItem>
          <MDBListGroupItem>Vestibulum at eros</MDBListGroupItem>
        </MDBListGroup>
      </MDBCard>
    </div>
  </main>
</template>
