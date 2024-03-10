<template>
  <div class="home">
    <age-range-chart></age-range-chart>
  </div>
</template>

<script>
import AgeRangeChart from '../components/AgeRangeChart.vue'

export default {
  name: 'HomeView',
  components: {
    AgeRangeChart
  }
}
</script>
