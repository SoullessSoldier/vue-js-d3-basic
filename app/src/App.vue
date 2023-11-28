<template>
  <h1>Using Vue 3 (Composition API) with D3</h1>
  <responsive-line-chart :data="data" />
  <div class="buttons">
    <button @click="addData">Add data</button>
    <button @click="filterData">Filter data</button>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import ResponsiveLineChart from "./components/ResponsiveLineChart.vue";
const data = ref<number[]>([]);

const addData = () => {
  // add random value from 0 to 50 to array
  data.value = [...data.value, Math.round(Math.random() * 50)];
};
const filterData = () => {
  data.value = data.value.filter((v) => v <= 35);
};
onMounted(() => {
  data.value = [10, 40, 15, 25, 50];
});
</script>


