<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-12 mt-2 mt-sm-1">
          <h1 class="text-center">Star Wars Planets</h1>
        </div>
        <div class="col-12">
          <div class="d-flex justify-content-between flex-column flex-sm-row">
            <b-button
              variant="success"
              @click="getPlanets"
              :disabled="tableIsLoading || !planets.isMore"
              :title="
                planets.isMore === null
                  ? 'There is no more data to retrieve'
                  : null
              "
            >
              Get 10 more planets
            </b-button>
            <b-button
              class="d-none d-sm-block"
              variant="dark"
              @click="fullWidth = !fullWidth"
            >
              {{ fullWidth ? "Container Width" : "Full Width" }}
            </b-button>
          </div>
        </div>
      </div>
    </div>
    <div
      id="sw-planets"
      :class="{ container: !fullWidth, 'container-fulid': fullWidth }"
    >
      <b-table
        class="mt-3 sw-table"
        :items="planets.items"
        :fields="fields"
        :busy="tableIsLoading"
        sticky-header="600px"
        sort-by="name"
        head-variant="dark"
        striped
        outlined
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
            >Show info</b-button
          >
        </template>
        <template #cell(residents)="row">
          <b-button
            v-if="row.item.residents.length"
            @click="getMoreInfo(row.item.residents, row)"
            >Show info</b-button
          >
        </template>
      </b-table>
      <p class="mt-3">
        Showing <b>{{ planets.items.length }}</b> of {{ planets.total }} planets
      </p>
      <b-modal
        ref="moreInfoModal"
        id="more-info-modal"
        :title="moreInfo.title"
        size="lg"
        hide-footer
        centered
      >
        <SwLoading v-if="moreInfo.isLoading" />
        <b-table stacked :items="moreInfo.content" :fields="moreInfo.fields" />
      </b-modal>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import SwLoading from "@/components/SwLoading.vue";

export default {
  data() {
    return {
      fullWidth: false,
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
      } catch (error) {
        const errorMessage = error?.message || "Something go wrong";
        this.$bvToast.toast(errorMessage, {
          title: "Error",
          toaster: "b-toaster-bottom-center",
          variant: "danger",
          solid: true
        });
        this.pagination -= 1;
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
      switch (row.field.key) {
        case "films":
          this.moreInfo.fields = [
            { key: "title" },
            { key: "director" },
            { key: "producer" },
            { key: "opening_crawl" },
            { key: "release_date" }
          ];
          break;
        case "residents":
          this.moreInfo.fields = [
            { key: "name" },
            { key: "birth_year" },
            { key: "eye_color" },
            { key: "gender" },
            { key: "hair_color" },
            { key: "height" },
            { key: "skin_color" },
            { key: "mass" }
          ];
          break;
      }
      try {
        const arrayOfPromises = arrayUrl.map(async url => {
          const apiResponse = await axios.get(url);
          return apiResponse.data;
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
