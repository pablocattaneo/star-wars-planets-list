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
    />
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data () {
    return {
      fields: [
        { key: 'name', sortable: true },
        { key: 'climate', sortable: true },
        { key: 'diameter', sortable: true },
        { key: 'films', sortable: true },
        { key: 'gravity', sortable: true },
        { key: 'orbital_period', sortable: true },
        { key: 'population', sortable: true },
        { key: 'residents', sortable: true },
        { key: 'rotation_period', sortable: true },
        { key: 'surface_water', sortable: true },
        { key: 'terrain', sortable: true },
        { key: 'created', sortable: true },
        { key: 'edited', sortable: true },
        { key: 'url', sortable: true },
      ],
      planets: []
    }
  },
  methods:{
    async getPlanets(){
      console.log("getPlanets");
      const apiResponse = await axios.get("https://swapi.dev/api/planets/")
      this.planets = apiResponse.data.results
    }
  },
  created(){
    this.getPlanets()
  }
}
</script>

<style>

</style>