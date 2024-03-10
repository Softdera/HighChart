<!-- AgeRangeChart.vue -->
<template>
    <div>
        <!--div id="container" style="width:100%; height:400px;">

        </div-->
        <highcharts :options="chartOptions"></highcharts>

    </div>
  </template>
  
  <script>
  import { Chart } from "highcharts-vue";

   

  export default {

    components: {
        highcharts: Chart,

    },
    data() {
      return {
        chartOptions: {
          chart: {
            type: 'column'
          },
          title: {
            text: 'Age Range of Voters'
          },
          xAxis: {
            categories: ['18-24', '25-34', '35-44', '45-54', '55-64', '65+']
          },
          yAxis: {
            title: {
              text: 'Number of Voters'
            }
          },
          series: [{
        name: 'Number of Voters',
        data: [1000, 2000, 3000, 2500, 1500, 800] // Example data, replace this with your actual data
        }]
    }
      };
    }
  }
  </script>
  
  <style scoped>
  /* Add your custom styles here */
  </style>
  