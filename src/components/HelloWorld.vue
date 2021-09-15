<template>
  <div>
    <div v-if="loading">Loading big data...</div>
    <div class="mb-4">
      <h4>Open</h4>
      <apexchart width="100%" type="bar" :options="options" :series="seriesOpen"></apexchart>
    </div>
    <div class="mb-4">
      <h4>Close</h4>
      <apexchart width="100%" type="bar" :options="options" :series="seriesClose"></apexchart>
    </div>
  </div>
</template>

<script>
import data from "../assets/data.json"
export default {
  name: 'HelloWorld',
  data () {
    return {
      loading: true,
      options: {
        chart: {
          id: 'vuechart-example'
        },
        xaxis: {
          categories: []
        }
      },
      seriesOpen: [
        {
          name: 'Average Open Torque',
          data: []
        },
        {
          name: 'Last Open Torque',
          data: []
        },
        {
          name: 'Forecast Open Torque',
          data: []
        },
      ],
      seriesClose: [
        {
          name: 'Average Open Torque',
          data: []
        },
        {
          name: 'Last Open Torque',
          data: []
        },
        {
          name: 'Forecast Open Torque',
          data: []
        },
      ]
    }
  },

  computed: {
    positions() {
      return [...new Set(data.map(item => item.Position))]
    },
    openData () {
      return data.filter(d => d.Direction === 'Open')
    },
    closeData () {
      return data.filter(d => d.Direction === 'Open')
    }
  },

  methods: {
    getOpenDataBasedPosition (dataObjectName, seriesObjectName) {
      let AOTData = []
      let LOTData = []
      let FOTData = []

      this.positions.forEach(position => {
        let totalAOT = 0
        let totalLOT = 0
        let totalFOT = 0
        this[dataObjectName].filter(d => d.Position === position).map((d) => {
          totalAOT+=d.AverageTorque
          totalLOT+=d.LastTorque
          totalFOT+=d.Profile
        })
        AOTData.push(totalAOT)
        LOTData.push(totalLOT)
        FOTData.push(totalFOT)
      })

      this[seriesObjectName] = [
        {
          name: 'Average Open Torque',
          data: AOTData
        },
        {
          name: 'Last Open Torque',
          data: LOTData
        },
        {
          name: 'Forecast Open Torque',
          data: []
        },
      ]
    }
  },

  mounted() {
    this.options = {
      xaxis: {
        tickAmount: 9,
        categories: this.positions
      }
    }
    setTimeout(() => {
      this.getOpenDataBasedPosition('openData', 'seriesOpen')
      this.getOpenDataBasedPosition('closeData', 'seriesClose')
      this.loading = false
    },1000)
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
