<template>
  <div class="home">
    <ul>
      <li v-bind:key="error.response.data.errors" v-for="error in errors">{{ error }}</li>
    </ul>

    <h1>All Places</h1>

    <form>
      <div class="form-group">
        <label for="place-name">Name</label>

        <input v-model="newName" class="form-control" id="place-name" />
      </div>
      <div class="form-group">
        <label for="place-address">Address</label>

        <input v-model="newAddress" class="form-control" id="place-address" />
      </div>
      <button type="submit" v-on:click="addPlace()" class="btn btn-primary">Add a new place</button>
    </form>

    <div v-bind:key="place.id" v-for="place in places">
      <div class="row">
        <div class="card column-lg-4" style="width: 18rem;">
          <div class="card-body">
            <h5 class="card-title">{{place.id}}. {{ place.name }}</h5>

            <button class="btn btn-primary card-link" v-on:click="showPlace(place)">Show Info</button>
          </div>
        </div>

        <div v-if="currentPlace === place">
          <p>Address: {{ place.address }}</p>
          <form>
            <div class="form-group">
              <div class="form-group">
                <label for="place-name">Name</label>

                <input v-model="place.name" class="form-control" id="place-name" />
              </div>
              <div class="form-group">
                <label for="place-name">Address</label>

                <input v-model="place.address" class="form-control" id="place-name" />
              </div>
            </div>
            <button class="btn btn-primary card-link" v-on:click="updatePlace(place)">Update Info</button>
            <button class="btn btn-primary card-link" v-on:click="destroyPlace(place)">Delete Place</button>
          </form>
        </div>
      </div>
      <hr />
    </div>
  </div>
</template>

<style>
form {
  width: 30%;
  margin: 2em 30em;
  justify-content: center;
  align-content: center;
}
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