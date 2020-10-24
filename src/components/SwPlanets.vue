<template>
  <div id="sw-planets">
    <b-table
      :items="planets"
      :fields="fields"
      sort-by="name"
      head-variant="dark"
      striped
      outlined
      responsive
    >
      <template #cell(films)="row">
        <b-button
          v-b-modal.filmModal
          @click="getFilmsWherePlanetAppear(row.item.films)"
          >Films</b-button
        >
      </template>
    </b-table>
    <b-modal id="filmModal" title="BootstrapVue">
      <div v-for="(film, index) in filmsTitles" :key="index">
        {{ film }}
      </div>
    </b-modal>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      filmsTitles: [],
      fields: [
        { key: "name", sortable: true },
        { key: "climate", sortable: true },
        { key: "diameter", sortable: true },
        { key: "films", sortable: true },
        { key: "gravity", sortable: true },
        { key: "orbital_period", sortable: true },
        { key: "population", sortable: true },
        { key: "residents", sortable: true },
        { key: "rotation_period", sortable: true },
        { key: "surface_water", sortable: true },
        { key: "terrain", sortable: true },
        { key: "created", sortable: true },
        { key: "edited", sortable: true },
        { key: "url", sortable: true }
      ],
      planets: []
    };
  },
  methods: {
    async getPlanets() {
      const apiResponse = await axios.get("https://swapi.dev/api/planets/");
      this.planets = apiResponse.data.results;
    },
    async getFilmsWherePlanetAppear(arrayUrl) {
      const arrayOfPromises = arrayUrl.map(async url => {
        const apiResponse = await axios.get(url);
        return apiResponse.data.title;
      });
      (async () => {
        const filmsTitles = await Promise.all(arrayOfPromises);
        this.filmsTitles = filmsTitles;
      })();
    }
  },
  created() {
    this.getPlanets();
  }
};
</script>

<style></style>
