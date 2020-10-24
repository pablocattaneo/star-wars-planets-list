<template>
  <div id="sw-planets">
    <div class="row">
      <div class="col-12 mt-5">
        <h1 class="text-center">Star Wars Planets</h1>
      </div>
    </div>
    <div class="row">
      <div class="col-12">
        <p>
          Screen is showing: {{ planets.items.length }} of
          {{ planets.total }} planets
        </p>
        <b-button
          variant="success"
          @click="getPlanets"
          :disabled="tableIsLoading || !planets.isMore"
        >
          Get 10 more planets
        </b-button>
      </div>
    </div>
    <b-table
      class="mt-4"
      :items="planets.items"
      :fields="fields"
      responsive
      sort-by="name"
      head-variant="dark"
      striped
      outlined
      :busy="tableIsLoading"
    >
      <template #table-busy>
        <div class="text-center text-primary my-2">
          <b-spinner class="align-middle"></b-spinner>
          <strong>Loading...</strong>
        </div>
      </template>
      <template #cell(films)="row">
        <b-button
          v-if="row.item.films.length"
          @click="getMoreInfo(row.item.films, row)"
          >More Info</b-button
        >
      </template>
      <template #cell(residents)="row">
        <b-button
          v-if="row.item.residents.length"
          @click="getMoreInfo(row.item.residents, row)"
          >More Info</b-button
        >
      </template>
    </b-table>
    <b-modal ref="moreInfoModal" id="more-info-modal" :title="moreInfo.title">
      <SwLoading v-if="moreInfo.isLoading" />
      <div v-for="(info, index) in moreInfo.content" :key="index">
        {{ info }}
      </div>
    </b-modal>
  </div>
</template>

<script>
import axios from "axios";
import SwLoading from "@/components/SwLoading.vue";

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
      planets: {
        total: 0,
        items: [],
        isMore: null
      },
      pagination: 0,
      tableIsLoading: true
    };
  },
  components: {
    SwLoading
  },
  methods: {
    async getPlanets() {
      try {
        this.tableIsLoading = true;
        this.pagination += 1;
        const apiResponse = await axios.get(
          `https://swapi.dev/api/planets/?page=${this.pagination}`
        );
        this.planets.items.push(...apiResponse.data.results);
        this.planets.isMore = apiResponse.data.next;
        this.planets.total = apiResponse.data.count;
      } finally {
        this.tableIsLoading = false;
      }
    },
    resetMoreInfo() {
      this.moreInfo.isLoading = true;
      this.moreInfo.title = "";
      this.moreInfo.content = [];
    },
    async getMoreInfo(arrayUrl, row) {
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
        this.moreInfo.content = await Promise.all(arrayOfPromises);
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
::v-deep
  #more-info-modal
    .modal-header
      text-transform: capitalize
    .modal-body
      min-height: 100px
</style>
