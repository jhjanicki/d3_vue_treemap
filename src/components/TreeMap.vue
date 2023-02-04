<template>
  <svg :width='length' :height='length'>
    <rect v-for="(d, i) in leaves" class="rect" :transform="`translate(${d.x0},${d.y0})`" :width="d.x1 - d.x0"
      :height="d.y1 - d.y0" :fill="colorScale(d.data[0])" stroke="white" stroke-width="0.5"></rect>
    <text v-for="(d, i) in leaves" class="text" :x="d.x0 + 3" :y="d.y0 + 15" font-size="14" font-weight="700"
      fill="black">{{`${d.data[0]}: ${d.value}`}}</text>
  </svg>
</template>

<script>
import * as d3 from "d3";
import data from '../assets/data.json'

export default {
  name: "TreeMap",
  data: () => ({
    legendData: [{ "category": "Critically Endangered", "color": "#b30000" }, { "category": "Endangered", "color": "#e34a33" }, { "category": "Vulnerable", "color": "#fc8d59" }, { "category": "Near Threatened", "color": "#fdbb84" }, { "category": "Least Concern", "color": "#fdd49e" }, { "category": "Data Deficient", "color": "#d9d9d9" }],
    length: 750
  }),
  computed: {
    data() {
      return data;
    },
    categories() {
      return this.legendData.map(d => d.category)
    },
    colors() {
      return this.legendData.map(d => d.color)
    },
    colorScale() {
      return d3.scaleOrdinal().domain(this.categories).range(this.colors);
    },
    leaves() {
      const data = this.data.sort((a, b) => this.categories.indexOf(a.redlistCategory) - this.categories.indexOf(b.redlistCategory));
      const hierarchyData = this.convertDataHierarchy(data);
      const treemap = d3.treemap()
        .paddingInner(2)
        .paddingOuter(1)
        .paddingTop(1)
        .round(true)
        .size([this.length, this.length])
        .tile(d3.treemapResquarify.ratio(2));
      return treemap(hierarchyData).leaves().sort(function (a, b) { return b.data[0] - a.data[0]; });
    }

  },
  methods: {
    convertDataHierarchy(data) {
      const groupingFn = [d => d.redlistCategory]; //can group by multiple attributes here 
      const rollupData = d3.rollup(data, v => v.length, ...groupingFn);
      const childrenAccessorFn = ([key, value]) => value.size && Array.from(value);
      return d3.hierarchy(rollupData, childrenAccessorFn)
        .sum(([key, value]) => value);
    },
  },
};

</script>

<style lang="css">
  .text {
    paint-order: stroke;
    stroke: white;
    stroke-width: 2px;
    stroke-linecap: butt;
    stroke-linejoin: miter;
  }
</style>