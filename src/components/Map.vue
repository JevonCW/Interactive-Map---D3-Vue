<template>
    <div id="map">
        <svg class="canvas" :viewBox="`0, 0, ${width}, ${height}`">
            <!-- SVG grouping element, applies common attributes to the appended paths -->
            <g class="pathGroup"></g>
        </svg>
        <div class="container">
            <text id="tooltip">{{ displayedCountry }}</text>
        </div>
        <Modal :selectedCountry="selectedCountry" @close="closeModal" />
    </div>
  </template>
  
  <script>
  import json from "@/assets/world.json";
  import { geoPath, geoEquirectangular } from "d3-geo";
  import * as selection from "d3-selection";
  import { feature } from "topojson-client";
  import Modal from "@/components/Modal.vue";
  
  export default {
    name: "Map",
    components: {
      Modal
    },
    data() {
      return {
        // Reads TopoJSON file and extracts country features
        myJson: feature(json, json.objects["countries"]),
        displayedCountry: "",
        selectedCountry: "",
        hoveredCountry: null,
        width: 900,
        height: 425
      };
    },
    computed: {
        // returns a geographic projection function for the svg map
        projection() {
        return geoEquirectangular().fitSize([this.width, this.height], this.myJson);
        },
        // returns a path generator function that will be used to draw the country borders on the map
        path() {
        return geoPath().projection(this.projection);
        },
        // svg grouping
        g() {
        return selection.select(".pathGroup");
        },
        svg() {
        return selection.select(".canvas");
        }
    },
    methods: {
      closeModal() {
        this.selectedCountry = "";
      },
    },
    mounted() {
        // for each feature in the myJson data object, append a path element to the pathgroup
      this.g
          .selectAll("path")
          .data(this.myJson.features)
          .enter()
          .append("path")
          .attr("class", "country")
          .attr("d", this.path)
          //define on hover events to return country name and change color of country
          .attr("fill", d => d === this.hoveredCountry ? "lightskyblue" : "aliceblue")
          .on("mouseover", (event, feature) => {
              this.hoveredCountry = feature;
              selection.select(event.target).attr("fill", "lightskyblue");
              this.displayedCountry = feature.properties.name;
          })
          //reset styling
          .on("mouseout", (event, feature) => {
              this.hoveredCountry = null;
              selection.select(event.target).attr("fill", "aliceblue");
              this.displayedCountry = "";
          })
          .on("click", (event, feature) => {
              this.selectedCountry = feature.properties.name;
          });
    }
  };
  </script>
  
  <style>
  .country {
    cursor: pointer;
    stroke: black;
    stroke-width: 0.3px;
  }
  .container {
    position: relative;
    display: flex;
    width: 600px; /* set a width for the container */
    margin: 0 auto; /* set margins to "auto" to center horizontally */
    justify-content: center;
    align-items: center;
  }
  #tooltip {
    font-size: 50px;
    font-weight: bold;
    fill: black;
    stroke: black;
    stroke-width: 0.25px;
  }
  </style>
  