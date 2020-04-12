<template>
  <div class="home">
    <ul>
      <li v-bind:key="error.response.data.errors" v-for="error in errors">{{ error }}</li>
    </ul>
    <h1>{{ message }}</h1>
    <h1>All Places</h1>
    <p>
      Name:
      <input v-model="newName" />
    </p>
    <p>
      Name:
      <input v-model="newAddress" />
    </p>
    <button v-on:click="addPlace()">Add a new place</button>
    <div v-bind:key="place.id" v-for="place in places">
      <p>{{place.id}}. Name: {{ place.name }}</p>
      <p>Address: {{ place.address }}</p>

      <hr />
    </div>
  </div>
</template>

<style>
</style>

<script>
import axios from "axios";
export default {
  data: function() {
    return {
      message: "Places App",
      errors: [],
      places: [],
      newName: "",
      newAddress: "",
    };
  },
  created: function() {
    axios
      .get("/api/places")
      .then(response => {
        console.log("Success", response.data);
        this.places = response.data;
      })
      .catch(error => {
        this.errors = error.response.data.errors;
      });
  },
  methods: {
    addPlace: function() {
      let params = {
        name: this.newName,
        address: this.newAddress,
      };
      axios
        .post("/api/places", params)
        .then(response => {
          console.log("Success", response.data);
          this.places.push(params);
          this.newName = "";
          this.newAddress = "";
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
  },
};
</script>