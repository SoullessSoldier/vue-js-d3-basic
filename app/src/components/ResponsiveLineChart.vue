<template>
  <div ref="resizeRef">
    <svg ref="svgRef">
      <g class="x-axis" />
      <g class="y-axis" />
    </svg>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref, watchEffect } from "vue";
import {
  select,
  line,
  scaleLinear,
  min,
  max,
  curveBasis,
  axisBottom,
  axisLeft,
} from "d3";
import useResizeObserver from "@/composables/resizeObserver";
export interface Props {
  data: any;
};
const props = defineProps<Props>();

const svgRef = ref(null);

const { resizeRef, resizeState } = useResizeObserver();

onMounted(() => {
  const svg = select(svgRef.value);
  watchEffect(() => {
    const { width, height } = resizeState.dimensions;
    const xScale = scaleLinear()
        .domain([0, props.data.length - 1]) // input values...
        .range([0, width]); //... output values
    const yScale = scaleLinear()
        .domain([min(props.data), max(props.data)]) // input values...
        .range([height, 0]); // ... output values

    // line generator: D3 method to transform an array of values to data points ("d") for a path element
    const lineGen = line()
        .curve(curveBasis)
        .x((value, index) => xScale(index))
        .y((value) => yScale(value));

    // render path element with D3's General Update Pattern
    svg
        .selectAll(".line") // get all "existing" lines in svg
        .data([props.data]) // sync them with our data
        .join("path") // create a new "path" for new pieces of data (if needed)

        // everything after .join() is applied to every "new" and "existing" element
        .attr("class", "line") // attach class (important for updating)
        .attr("stroke", "green") // styling
        .attr("d", lineGen); // shape and form of our line!

    // render axes with help of scales
    // (we let Vue render our axis-containers and let D3 populate the elements inside it)
    const xAxis = axisBottom(xScale);
    svg
        .select(".x-axis")
        .style("transform", `translateY(${height}px)`) // position on the bottom
        .call(xAxis);

    const yAxis = axisLeft(yScale);
    svg.select(".y-axis").call(yAxis);

    //return { svgRef, resizeRef };
  })
});

</script>

<style scoped>

</style>