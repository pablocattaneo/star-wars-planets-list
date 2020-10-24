<template>
  <div id="sw-loading" class="text-center">
    <b-spinner
      variant="primary"
      class="sw-loading-spinner"
      style="width: 3rem; height: 3rem;"
    />
  </div>
</template>

<style lang="sass" scoped>
#sw-loading
  position: absolute
  left: 0
  right: 0
  top: 0
  bottom: 0
  margin: auto
  z-index: 999999999
  background: white
  .sw-loading-spinner
    position: absolute
    display: block
    left: 0
    right: 0
    top: 0
    bottom: 0
    margin: auto
    z-index: 10
</style>
