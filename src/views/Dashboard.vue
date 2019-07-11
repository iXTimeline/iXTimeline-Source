<template>
  <v-container
    fill-height
    fluid
    grid-list-xl
  >
    <v-layout wrap>
      <v-flex
        xs10
        offset-xs1
      >
      <div id="dashboard" style="width:1200px;">
          <div id="chart" style="position: relative; width: 1200px; height: 600px; backgroundColor: #ffffff;"></div>
          <div id="control"></div>
      </div>
      <div id="junk_div" style="display: none;"></div>
    </v-flex>
      <v-flex
        sm6
        xs12
        md6
        lg2
        offset-lg3
      >
        <material-stats-card
          color="green"
          icon="mdi-check"
          title="Successful jobs"
          value="34,245"
          sub-icon="mdi-calendar"
          sub-text="Last 24 Hours"
        />
      </v-flex>
      <v-flex
        sm6
        xs12
        md6
        lg2
      >
        <material-stats-card
          color="orange"
          icon="mdi-content-copy"
          title="Storage"
          value="49/50"
          small-value="GB"
          sub-icon="mdi-information"
          sub-text="Storage nearing capacity."
        />
      </v-flex>
      <v-flex
        sm6
        xs12
        md6
        lg2
      >
        <material-stats-card
          color="red"
          icon="mdi-information-outline"
          title="Failed jobs"
          value="75"
          sub-icon="mdi-alert"
          sub-icon-color="error"
          sub-text="Issues needing attention."
        />
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import processData from '../assets/PROCESS.json'
import registryData from '../assets/REGISTRY.json'

export default {
  data () {
    return {
      chartData: [],
      chartOptions: {
        hAxis: {
          minValue: '',
          maxValue: ''
        }
      },
      processData: processData,
      registryData: registryData,
      activeData: [3719751],
      tabs: 0,
      list: {
        0: false,
        1: false,
        2: false
      }
    }
  },
  mounted: function() {
    console.log("hello");
    document.onreadystatechange = () => {
  if (document.readyState == "complete") {
      // run code here
      this.drawDashboard();
  }
}
  },
  methods: {
    drawDashboard() {

                var control = new google.visualization.ControlWrapper({
    controlType: 'ChartRangeFilter',
    containerId: 'control',
    options: {
        // Filter by the date axis.
        filterColumnIndex: 1,
        ui: {
            chartType: 'LineChart',
            minRangeSize: 600000,
            chartOptions: {
                width: 1200,
                height: 70,
                hAxis: {
                    format: 'hh:mm:ss',
                    ticks: [
                        this.chartOptions.hAxis.minValue,

                        this.chartOptions.hAxis.maxValue
                    ]
                },
                chartArea: {
                  width: '90%', // make sure this is the same for the chart and control so the axes align right
                  height: '90%'
                }
            },
            chartView: {
                columns: [1, {
                    type: 'number',
                    calc: function () {return 0;}
                }]
            }
        }
    }
});

var dashboard = new google.visualization.Dashboard(
    document.getElementById('dashboard'));

                var chart = new google.visualization.ChartWrapper({
                    'chartType': 'Timeline',
                        'containerId': 'chart',
                        'options': {
                        'width': 1200,
                            'height': 600,
                            'chartArea': {
                            width: '100%', // make sure this is the same for the chart and control so the axes align right
                            height: '100%'
                        },
                            'backgroundColor': '#ffd'
                    },
                        'view': {
                        'columns': [0, 1, 2]
                    }

                });

                var data = new google.visualization.arrayToDataTable(this.chartData);

                dashboard.bind(control, chart);
                dashboard.draw(data);
            },
    refactorProcess() {
      console.log('in refactor')
      var tempProcess = {}
      for (var i in this.processData) {
        var properties = {}
        var registryID = ''
        var tempArray = []
        for (var j in this.processData[i]) {
            registryID = this.processData[i][j].RegistryID
            if (tempProcess.hasOwnProperty(registryID)) {
              properties[j] = this.processData[i][j]
              tempProcess[registryID].push(properties[j])
            } else {
              properties[j] = this.processData[i][j]
              tempArray.push(properties[j])
              tempProcess[registryID] = tempArray
            }
            tempArray = []
        }
      }
      this.processData = tempProcess
    },
    refactorRegistry() {
      console.log('in refactor registry')
      var tempRegistry = {}
      for (var i in this.registryData) {
        var properties = {}
        var registryID = ''
        for (var j in this.registryData[i]) {
            registryID = this.registryData[i][j].RegistryID
            properties[j] = this.registryData[i][j]
            tempRegistry[registryID] = properties[j]
        }
      }
      this.registryData = tempRegistry
    },
    calcRegistryBeginEndTime() {
      var registryID = ''
      var minProcessBeginArray = []
      var maxProcessEndArray = []

      for (var i in this.registryData) {
        registryID = i
        for (var j = 0; j < this.processData[registryID].length; j++) {
          minProcessBeginArray.push(Date.parse(this.processData[registryID][j].ProcessBeginTime))
          maxProcessEndArray.push(Date.parse(this.processData[registryID][j].ProcessEndTime))
        }
        if (minProcessBeginArray.length > 0) {
          this.registryData[registryID].BeginTime = Math.min(...minProcessBeginArray)

        }
        if (maxProcessEndArray.length > 0) {
          this.registryData[registryID].EndTime = Math.max(...maxProcessEndArray)

        }

        maxProcessEndArray = []
        minProcessBeginArray = []
      }
    },
    calcChartRange() {
      var chartMin = []
      var chartMax = []

      for (var i in this.registryData) {
        console.log(this.registryData[i].BeginTime)
        chartMin.push(this.registryData[i].BeginTime)
        chartMax.push(this.registryData[i].EndTime)
      }

      this.chartOptions.hAxis.minValue = new Date(Math.min(...chartMin))
      this.chartOptions.hAxis.maxValue = new Date(Math.max(...chartMax))

    }
  },
  created: function() {
    this.refactorProcess()
    this.refactorRegistry()
    this.calcRegistryBeginEndTime()
    this.calcChartRange()

    for (var i in this.registryData) {
      this.chartData.push([ this.registryData[i].RegistryName, new Date(this.registryData[i].BeginTime), new Date(this.registryData[i].EndTime) ])
    }

    console.log(this.chartData)
    console.log(this.chartOptions.hAxis.minValue.getHours())
  }
}
</script>
<style>
</style>
