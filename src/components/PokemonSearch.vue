<script>
import { MDBInput, MDBBtn, MDBIcon } from "mdb-vue-ui-kit";
import axios from "axios";

export default {
  components: {
    MDBInput,
    MDBBtn,
    MDBIcon,
  },
  data() {
    return {
      input_search: "",
      poke_data: "",
      show_error: false,
    };
  },
  methods: {
    async sendName() {
      try {
        const response = await axios.get(
          `https://pokeapi.co/api/v2/pokemon/${this.input_search.toLowerCase()}`
        );
        this.poke_data = response.data.abilities[0].ability.name;
        this.show_error = false;
      } catch (error) {
        console.error(error);
        this.poke_error = null;
        this.show_error = true;
      }
    },
  },
};
</script>

<template>
  <MDBInput
    v-model="input_search"
    inputGroup
    :formOutline="false"
    wrapperClass="mb-3"
    placeholder="Search"
    aria-label="Search"
    aria-describedby="button-addon2"
  >
    <MDBBtn color="primary" v-on:click="sendName">
      <MDBIcon icon="search" />
    </MDBBtn>
  </MDBInput>
  <div v-if="show_error && !poke_data">Pokemon not found</div>
</template>
