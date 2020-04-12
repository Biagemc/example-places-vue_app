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

      <button v-on:click="showPlace(place)">Show Info</button>

      <div v-if="currentPlace === place">
        <p>Address: {{ place.address }}</p>
        <p>
          Name:
          <input v-model="place.name" />
        </p>
        <p>
          Name:
          <input v-model="place.address" />
        </p>
        <button v-on:click="updatePlace(place)">Update Info</button>
        <button v-on:click="destroyPlace(place)">Delete Place</button>
      </div>

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
      currentPlace: "",
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
          console.log("Successfuly Created.", response.data);
          this.places.push(params);
          this.newName = "";
          this.newAddress = "";
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function(thePlace) {
      this.currentPlace = thePlace;
    },
    updatePlace: function(thePlace) {
      let params = {
        name: thePlace.name,
        address: thePlace.address,
      };
      axios
        .patch("/api/places/" + thePlace.id, params)
        .then(response => {
          console.log("Successfuly updated...", response.data);
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
    destroyPlace: function(thePlace) {
      axios
        .delete("/api/places/" + thePlace.id)
        .then(response => {
          console.log("Place Successfuly deleted!", response.data);
          let index = this.places.indexOf(thePlace);
          this.places.splice(index, 1);
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
  },
};
</script>