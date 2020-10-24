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
        <b-button v-if="row.item.films.length" @click="getMoreInfo(row.item.films, row)">More Info</b-button>
      </template>
      <template #cell(residents)="row">
        <b-button v-if="row.item.residents.length" @click="getMoreInfo(row.item.residents, row)"
          >More Info</b-button
        >
      </template>
    </b-table>
    <b-modal ref="moreInfoModal" id="more-info-modal" :title="moreInfo.title">
      <div v-if="moreInfo.isLoading" class="more-info-modal-is-loading-wrapper">
        <b-spinner
          class="more-info-modal-is-loading"
          label="Loading..."
        ></b-spinner>
      </div>
      <div v-for="(info, index) in moreInfo.content" :key="index">
        {{ info }}
      </div>
    </b-modal>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      moreInfo: {
        isLoading: true,
        content: [],
        title: ""
      },
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
    resetMoreInfo() {
      this.moreInfo.isLoading = true;
      this.moreInfo.title = "";
      this.moreInfo.content = [];
    },
    getMoreInfo(arrayUrl, row) {
      this.resetMoreInfo();
      this.$refs["moreInfoModal"].show();

      this.moreInfo.title = row.field.key;

      let dataToShow;
      switch (row.field.key) {
        case "films":
          dataToShow = "title";
          break;
        case "residents":
          dataToShow = "name";
          break;
      }
      try {
        const arrayOfPromises = arrayUrl.map(async url => {
          const apiResponse = await axios.get(url);
          return apiResponse.data[dataToShow];
        });
        (async () => {
          this.moreInfo.content = await Promise.all(arrayOfPromises);
        })();
      } finally {
        this.moreInfo.isLoading = false;
      }
    }
  },
  created() {
    this.getPlanets();
  }
};
</script>

<style lang="sass" scoped>
#more-info-modal
  .more-info-modal-is-loading-wrapper
    position: absolute
    top: 0
    right: 0
    bottom: 0
    left: 0
    background: white
    .more-info-modal-is-loading
      position: absolute
      top: 0
      right: 0
      bottom: 0
      left: 0
      background: white
      margin: auto
      z-index: 1
::v-deep
  #more-info-modal
    .modal-header
      text-transform: capitalize
    .modal-body
      min-height: 100px
</style>
