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

          <GChart
            :settings="{ packages: ['timeline'] }"
            type="Timeline"
            :data="chartData"
            :options="chartOptions"
          />
    </v-flex>
      <!--
      <v-flex
        md12
        sm12
        lg4
      >


        <material-chart-card
          :data="emailsSubscriptionChart.data"
          :options="emailsSubscriptionChart.options"
          :responsive-options="emailsSubscriptionChart.responsiveOptions"
          color="red"
          type="Bar"
        >
          <h4 class="title font-weight-light">Email Subscription</h4>
          <p class="category d-inline-flex font-weight-light">Last Campaign Performance</p>

          <template slot="actions">
            <v-icon
              class="mr-2"
              small
            >
              mdi-clock-outline
            </v-icon>
            <span class="caption grey--text font-weight-light">updated 10 minutes ago</span>
          </template>
        </material-chart-card>
      </v-flex>

      <v-flex
        md12
        sm12
        lg4
      >
        <material-chart-card
          :data="dataCompletedTasksChart.data"
          :options="dataCompletedTasksChart.options"
          color="green"
          type="Line"
        >
          <h3 class="title font-weight-light">Completed Tasks</h3>
          <p class="category d-inline-flex font-weight-light">Last Last Campaign Performance</p>

          <template slot="actions">
            <v-icon
              class="mr-2"
              small
            >
              mdi-clock-outline
            </v-icon>
            <span class="caption grey--text font-weight-light">campaign sent 26 minutes ago</span>
          </template>
        </material-chart-card>
      </v-flex>
      -->
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
      <!--
      <v-flex
        sm6
        xs12
        md6
        lg3
      >
        <material-stats-card
          color="info"
          icon="mdi-twitter"
          title="Followers"
          value="+245"
          sub-icon="mdi-update"
          sub-text="Just Updated"
        />
      </v-flex>
      <v-flex
        md12
        lg6
      >
        <material-card
          color="orange"
          title="Employee Stats"
          text="New employees on 15th September, 2016"
        >
          <v-data-table
            :headers="headers"
            :items="items"
            hide-actions
          >
            <template
              slot="headerCell"
              slot-scope="{ header }"
            >
              <span
                class="font-weight-light text-warning text--darken-3"
                v-text="header.text"
              />
            </template>
            <template
              slot="items"
              slot-scope="{ index, item }"
            >
              <td>{{ index + 1 }}</td>
              <td>{{ item.name }}</td>
              <td class="text-xs-right">{{ item.salary }}</td>
              <td class="text-xs-right">{{ item.country }}</td>
              <td class="text-xs-right">{{ item.city }}</td>
            </template>
          </v-data-table>
        </material-card>
      </v-flex>
      <v-flex
        md12
        lg6
      >
        <material-card
          class="card-tabs"
          color="green">
          <v-flex
            slot="header"
          >
            <v-tabs
              v-model="tabs"
              color="transparent"
              slider-color="white"
            >
              <span
                class="subheading font-weight-light mr-3"
                style="align-self: center"
              >Tasks:</span>
              <v-tab class="mr-3">
                <v-icon class="mr-2">mdi-bug</v-icon>
                Bugs
              </v-tab>
              <v-tab class="mr-3">
                <v-icon class="mr-2">mdi-code-tags</v-icon>
                Website
              </v-tab>
              <v-tab>
                <v-icon class="mr-2">mdi-cloud</v-icon>
                Server
              </v-tab>
            </v-tabs>
          </v-flex>

          <v-tabs-items v-model="tabs">
            <v-tab-item
              v-for="n in 3"
              :key="n"
            >
              <v-list three-line>
                <v-list-tile @click="complete(0)">
                  <v-list-tile-action>
                    <v-checkbox
                      :value="list[0]"
                      color="green"
                    />
                  </v-list-tile-action>
                  <v-list-tile-title>
                    Sign contract for "What are conference organized afraid of?"
                  </v-list-tile-title>
                  <div class="d-flex">
                    <v-tooltip
                      top
                      content-class="top">
                      <v-btn
                        slot="activator"
                        class="v-btn--simple"
                        color="success"
                        icon
                      >
                        <v-icon color="primary">mdi-pencil</v-icon>
                      </v-btn>
                      <span>Edit</span>
                    </v-tooltip>
                    <v-tooltip
                      top
                      content-class="top">
                      <v-btn
                        slot="activator"
                        class="v-btn--simple"
                        color="danger"
                        icon
                      >
                        <v-icon color="error">mdi-close</v-icon>
                      </v-btn>
                      <span>Close</span>
                    </v-tooltip>

                  </div>
                </v-list-tile>
                <v-divider/>
                <v-list-tile @click="complete(1)">
                  <v-list-tile-action>
                    <v-checkbox
                      :value="list[1]"
                      color="success"
                    />
                  </v-list-tile-action>
                  <v-list-tile-title>
                    Lines From Great Russian Literature? Or E-mails From My Boss?
                  </v-list-tile-title>
                  <div class="d-flex">
                    <v-tooltip
                      top
                      content-class="top">
                      <v-btn
                        slot="activator"
                        class="v-btn--simple"
                        color="success"
                        icon
                      >
                        <v-icon color="primary">mdi-pencil</v-icon>
                      </v-btn>
                      <span>Edit</span>
                    </v-tooltip>

                    <v-tooltip
                      top
                      content-class="top">
                      <v-btn
                        slot="activator"
                        class="v-btn--simple"
                        color="danger"
                        icon>
                        <v-icon color="error">mdi-close</v-icon>
                      </v-btn>
                      <span>Close</span>
                    </v-tooltip>
                  </div>
                </v-list-tile>
                <v-divider/>
                <v-list-tile @click="complete(2)">
                  <v-list-tile-action>
                    <v-checkbox
                      :value="list[2]"
                      color="success"
                    />
                  </v-list-tile-action>
                  <v-list-tile-title>
                    Flooded: One year later, assessing what was lost and what was found when a ravaging rain swept through metro Detroit
                  </v-list-tile-title>
                  <div class="d-flex">
                    <v-tooltip
                      top
                      content-class="top">
                      <v-btn
                        slot="activator"
                        class="v-btn--simple"
                        color="success"
                        icon
                      >
                        <v-icon color="primary">mdi-pencil</v-icon>
                      </v-btn>
                      <span>Edit</span>
                    </v-tooltip>
                    <v-tooltip
                      top
                      content-class="top">
                      <v-btn
                        slot="activator"
                        class="v-btn--simple"
                        color="danger"
                        icon
                      >
                        <v-icon color="error">mdi-close</v-icon>
                      </v-btn>
                      <span>Close</span>
                    </v-tooltip>

                  </div>
                </v-list-tile>
              </v-list>
            </v-tab-item>
          </v-tabs-items>
        </material-card>
      </v-flex>
    -->
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
        chart: {
          title: 'Company Performance',
          subtitle: 'Sales, Expenses, and Profit: 2014-2017',
        },
        height: 500,
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
  methods: {
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
          console.log(registryID + ' minarray ' + minProcessBeginArray)
          console.log(Math.min(...minProcessBeginArray))
        }
        if (maxProcessEndArray.length > 0) {
          this.registryData[registryID].EndTime = Math.max(...maxProcessEndArray)
          console.log(registryID + ' maxarray ' + maxProcessEndArray)
          console.log(Math.min(...maxProcessEndArray))
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

      this.chartOptions.hAxis.minValue = new Date(Math.min(...chartMin) - 10000)
      this.chartOptions.hAxis.maxValue = new Date(Math.max(...chartMax) + 10000)

    }
  },
  created: function() {
    this.refactorProcess()
    this.refactorRegistry()
    this.calcRegistryBeginEndTime()
    this.calcChartRange()
    console.log(registryData)
    this.chartData = [
          ['President' , 'start', 'end']
      ]

    for (var i in this.registryData) {
      this.chartData.push([ this.registryData[i].RegistryName, new Date(this.registryData[i].BeginTime), new Date(this.registryData[i].EndTime) ])
    }



  }
}
</script>
<style>
</style>
