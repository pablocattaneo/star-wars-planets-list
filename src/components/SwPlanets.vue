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
        <b-button v-b-modal.filmModal @click="getMoreInfo(row.item.films, row)"
          >Films</b-button
        >
      </template>
      <template #cell(residents)="row">
        <b-button
          v-b-modal.filmModal
          @click="getMoreInfo(row.item.residents, row)"
          >Films</b-button
        >
      </template>
    </b-table>
    <b-modal id="filmModal" title="BootstrapVue">
      <div v-for="(film, index) in moreInfo" :key="index">
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
      moreInfo: [],
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
        { key: "edited", sortable: true }
      ],
      planets: []
    };
  },
  methods: {
    async getPlanets() {
      const apiResponse = await axios.get("https://swapi.dev/api/planets/");
      this.planets = apiResponse.data.results;
    },
    getMoreInfo(arrayUrl, row) {
      let dataToShow;
      switch (row.field.key) {
        case "films":
          dataToShow = "title";
          break;
        case "residents":
          dataToShow = "name";
          break;
        default:
          break;
      }
      const arrayOfPromises = arrayUrl.map(async url => {
        const apiResponse = await axios.get(url);
        return apiResponse.data[dataToShow];
      });
      (async () => {
        this.moreInfo = await Promise.all(arrayOfPromises);
      })();
    }
  },
  created() {
    this.getPlanets();
  }
};
</script>

<style></style>
