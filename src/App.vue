<template>
  <div id="app" class="container">
    <div class="row">
      <div class="col-12">
        <SwPlanets />
      </div>
    </div>
  </div>
</template>

<script>
import SwPlanets from "./components/SwPlanets.vue";

export default {
  name: "App",
  components: {
    SwPlanets
  }
};
</script>
