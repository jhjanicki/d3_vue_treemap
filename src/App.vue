<template>

  <div id="chart">
    <TreeMap />
  </div>

</template>

<script>
import TreeMap from './components/TreeMap.vue'


export default {
  name: 'app',
  components: {
    TreeMap,
  },
}

</script>


<style lang="css">
  #chart {
    text-align: center;
    font-family: 'Source Sans Pro', sans-serif;
  }
</style>
