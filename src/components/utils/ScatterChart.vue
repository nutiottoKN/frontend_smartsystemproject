<template>
  <canvas ref="myChart" width="700px" height="350px"></canvas>
</template>

<script>
  import Chart from 'vue-chartjs'

  export default {
    props: {
        chartdata: {
            type: Array
        }
    },
    /*data() {
      return {
        data: {
          datasets: [{
            label: 'Population Data',
            borderWidth: 1,
            borderColor: '#2554FF',
            backgroundColor: '#2554FF',
            data: this.chartdata
          }]
        },
        options: {
          legend: {
            display: true
          },
          responsive: true,
          maintainAspectRatio: false
        }
      }
    },
    mounted() {
      this.renderChart(this.chartData, this.options)
    },*/
     async mounted () {
        await new Chart(this.$refs.myChart, {
            type: 'scatter',
            data: {
                 datasets: [{
            label: 'Population Data',
            borderWidth: 1,
            borderColor: '#2554FF',
            backgroundColor: '#2554FF',
            data: this.chartdata
          }]
        },
        options: {
          legend: {
            display: true
          },
          responsive: true,
          maintainAspectRatio: false
        }
         
    });
    }
  }
</script>
