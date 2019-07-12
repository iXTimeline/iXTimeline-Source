<template>
    <v-container fill-height fluid grid-list-xl>
        <v-toolbar color="purple darken-1" dark fixed app clipped-right>
            <v-toolbar-title class="font-italic display-2">iXTimeline</v-toolbar-title>
            <v-spacer/>
        </v-toolbar>
        <v-dialog v-model="modalVisible">
            <v-card>
                <v-container grid-list-md text-xs-left>
                    <v-layout id="task-modal-layout" row wrap justify-center>
                        <v-flex xs12>
                            <div id="task-modal-dashboard">
                            </div>
                        </v-flex>
                        <v-flex wrap xs4>
                            <v-card>
                                <v-card-text v-html="processText"/>
                            </v-card>
                        </v-flex>
                        <v-flex wrap xs4>
                            <v-card>
                                <v-card-text v-html="taskText"/>
                            </v-card>
                        </v-flex>
                        <v-flex wrap xs4>
                            <v-card>
                                <v-card-text>
                                </v-card-text>
                            </v-card>
                        </v-flex>
                    </v-layout>
                </v-container>
            </v-card>
        </v-dialog>
        <v-navigation-drawer fixed right clipped app width="460">
            <v-list>
                <v-list-tile class='headertile'>
                    <v-list-tile-content>
                        <v-list-tile-title>FILTERS</v-list-tile-title>
                    </v-list-tile-content>
                </v-list-tile>
                <v-flex xs12 px-3>
                    <v-list-tile-content style="min-width: 500px;">
                        <v-select :items="itemTypes" label="Item Type" chips deletable-chips multiple clearable
                            v-model="activeItemTypeFilters" style="min-width: 425px; max-width: 425px">
                        </v-select>
                    </v-list-tile-content>
                </v-flex>
                <v-flex xs12 px-3>
                    <v-list-tile-content style="min-width: 500px;">
                        <v-select :items="databases" label="Database" chips deletable-chips multiple clearable
                            v-model="activeDatabaseFilters"style="min-width: 425px; max-width: 425px">
                        </v-select>
                    </v-list-tile-content>
                </v-flex>
                <v-flex xs12 px-3>
                    <v-list-tile-content style="min-width: 500px;">
                        <v-select :items="servers" label="Server" chips deletable-chips multiple clearable
                            v-model="activeServerFilters" style="min-width: 425px; max-width: 425px">
                        </v-select>
                    </v-list-tile-content>
                </v-flex>
                <v-layout row pl-3 style="position: absolute; bottom:20px">
                    <v-flex xs4 offset-xs2 mr-5>
                        <v-btn color="success" @click="applyFilters()">Apply</v-btn>
                    </v-flex>
                    <v-flex xs4 ml-5>
                        <v-btn color="error" @click="resetFilters()">Clear All</v-btn>
                    </v-flex>
                    <v-spacer/>
                </v-layout>
            </v-list>
        </v-navigation-drawer>
        <v-layout wrap>
            <v-flex xs11 offset-xs1 class="display-2">
                <span pr-0>{{selectedDate}}</span>
                <v-menu offset-y>
                    <template v-slot:activator="{ on }">
                        <v-btn color="primary" dark flat icon v-on="on" >
                            <v-icon>calendar_today</v-icon>
                        </v-btn>
                    </template>
                    <v-date-picker v-model="selectedDate" landscape reactive></v-date-picker>
                </v-menu>
            </v-flex>
            <v-flex xs10 offset-xs1 v-if="drawTimeline">
                <div id="dashboard" style="width:1200px;">
                    <div id="chart" style="position: relative; width: 1200px; height: 600px; backgroundColor: #ffffff;"></div>
                    <div id="control"></div>
                </div>
                <div id="junk_div" style="display: none;"></div>
            </v-flex>
            <v-spacer/>
            <v-flex xs10 offset-xs1 v-if="!drawTimeline">
                <v-card color="red lighten-1">
                    <v-card-text>
                        <v-icon dark>report_problem</v-icon>
                        <span class="white--text pl-3" >No results found.</span>
                    </v-card-text>
                </v-card>
            </v-flex>
            <v-spacer/>
        </v-layout>
    </v-container>
</template>

<script>
import processData from '../assets/PROCESS.json'
import registryData from '../assets/REGISTRY.json'
import parentRegistryData from '../assets/parentregistryfinal.json'
import childRegistryData from '../assets/ChildRegistries.json'
//import parentProcessData from '../assets/parentprocessfinal.json'
import childProcessData from '../assets/ChildProcess.json'
import axios from 'axios';

export default {
    data () {
        return {
            filters: {
                itemtype: {
                    account: false,
                    audit: false,
                    bill: false,
                    business_alert: false,
                    CDR_file: false,
                    connect: false,
                    cycle: false,
                    data_file: false,
                    database: false,
                    external_data_warehouse: false,
                    extract: false,
                    finance: false,
                    import_export: false,
                    infrastructure_test: false,
                    ixinsight: false,
                    notification: false,
                    performance_metric: false,
                    route_guide: false,
                    standardized_CDR: false,
                    switch_upload: false,
                    system_audit: false,
                    trade: false,
                    write_off: false
                }
            },
            itemTypes: [
                'All',
                'Account',
                'Audit',
                'Bill',
                'Business Alert',
                'CDR File',
                'Connect',
                'Cycle',
                'Data File',
                'Database',
                'External Data Warehouse',
                'Extract',
                'Finance',
                'Import/Export',
                'Infrastructure Test',
                'iXInsight',
                'Notification',
                'Performance Metric',
                'Route Guide',
                'Standardized CDR',
                'Switch Upload',
                'System Audit',
                'Trade',
                'Write Off'
            ],
            databases: [
              'iXAdmin_Audit',
              'iXAdmin_Main',
              'iXAdmin_Port',
              'iXAdmin_Report',
              'iXAlert_Audit',
              'iXAlert_Main',
              'iXAlert_Mart',
              'iXAudit_Audit',
              'iXAudit_Main',
              'iXAudit_Port',
              'iXControl_Audit',
              'iXControl_Client',
              'iXControl_Main',
              'iXControl_Port',
              'iXControl_Report',
              'iXControl_Utilities',
              'iXCore_Audit',
              'iXCore_Main',
              'iXCore_Port',
              'iXMart',
              'iXReport_Audit',
              'iXReport_Main',
              'iXReport_Port',
              'iXRoute_Audit',
              'iXRoute_Main',
              'iXRoute_Port',
              'iXRoute_Report',
              'iXSubscription_Main',
              'iXSum',
              'iXTrade_Main',
              'iXTrade_Port',
              'iXTrade_Audit',
              'iXAEP',
              'iXTranslate_Main',
              'iXTranslate_Port',
              'iXBill_Audit',
              'iXBill_Main',
              'iXBill_Port',
              'iXAudit_Report',
              'iXCDR_01',
              'iXCDR_02',
              'iXCDR_03',
              'iXCDR_04',
              'iXCDR_05',
              'iXCDR_06',
              'iXCDR_07',
              'iXCDR_08',
              'iXCDR_nnn',
              'iXTemp',
              'iXTranslate_Audit',
              'iXUpload_Main',
              'iXFinance_Main',
              'iXFinance_Port_FT',
              'iXQOS',
              'iXInsight_Main',
              'iXInsight_Mart',
              'iXFinance_Audit',
              'iXFinance_Port_ATT',
              'iXFinance_Port',
              'iXArchive',
              'iXMart_CDR',
              'iXDataLoader_INCR',
              'iXDataLoader_INIT',
              'iXDataLoader_Main',
              'iXSubscriber_Main',
              'iXSubscriber_Audit'
            ],
            servers: [
              'CoreServer',
              'ReportServer',
              'BatchServer1',
              'CDRServer01',
              'CDRServer02'
            ],
            drawTimeline: true,
            apiGET: {},
            selectedDate: '2019-05-28',
            displayCalendar: false,
            isLoading: true,
            activeItemTypeFilters: [],
            activeDatabaseFilters: [],
            activeServerFilters: [],
            chartData: [],
            parentRegistryData: parentRegistryData,
            childRegistryData: childRegistryData,
            parentProcessData: [],
            childProcessData: childProcessData,
            chartOptions: {
                hAxis: {
                    minValue: new Date('2019-06-01'),
                    maxValue: new Date('2019-06-02')
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
            },
            taskChartWidth: 0,
            modalVisible: false,
            processText: "",
            taskChartData: [],
            taskText: "",
            windowHeight: 0,
            windowWidth: 0
        }
    },
    mounted: function() {
        google.load("visualization", "1", {packages:["timeline"]});
        this.$nextTick(function() {
            window.addEventListener('resize', this.getWindowWidth);
            window.addEventListener('resize', this.getWindowHeight);

            //Init
            this.getWindowWidth();
            this.getWindowHeight();
        });

        this.taskChartData.push([
            {id: 'Process', label: 'Process', type: 'string'},
            {id: 'Task', label: 'Task', type: 'string'},
            {id: 'Start', label: 'Start', type: 'date'},
            {id: 'End', label: 'End', type: 'date'}
        ]);

        axios
          .get('https://92r3xgypt5.execute-api.us-east-2.amazonaws.com/dev/tomia')
          .then( resp => {
            this.parentProcessData = resp.data
            this.isLoading = true
            this.refactorParentRegistry()
            this.refactorChildRegistry()
            this.refactorParentProcess()
            this.refactorChildProcess()

            for (var i in this.parentProcessData) {
                for (var j in this.parentProcessData[i]) {
                    //console.log(this.parentProcessData[i][j].Process)
                    this.chartData.push([ this.parentProcessData[i][j].Process, String(this.parentProcessData[i][j].ProcessID), new Date(this.parentProcessData[i][j].ProcessBeginTime), new Date(this.parentProcessData[i][j].ProcessEndTime), String(this.parentProcessData[i][j].ProcessID) ])
                }
            }
            //console.log(this.chartData)
            this.isLoading = false
          }

          ).catch((err) => {
          console.log('error')
        });
    },
    watch: {
        isLoading: function() {
            this.drawDashboard();
        }
    },
    methods: {
        drawDashboard() {
            var chart = new google.visualization.Timeline(document.getElementById('chart'));
            var data = new google.visualization.DataTable();
            var vm = this;

            data.addColumn({ type: 'string', id: 'Process' });
            data.addColumn({ type: 'string', id: 'Runtime' });
            data.addColumn({ type: 'date', id: 'Start' });
            data.addColumn({ type: 'date', id: 'End' });
            data.addColumn({ type: 'string', role: 'tooltip'});
            data.addRows(this.chartData);

            var options = {
                timeline: {
                    showBarLabels: false,
                }
            };

            google.visualization.events.addListener(chart, 'select', function() {
                var selection = chart.getSelection();

                if (selection.length > 0) {
                    vm.showModal(data.getValue(selection[0].row, 4));
                }
            });

          //dashboard.bind(control, chart);
            chart.draw(data, options);
        },
        refactorParentProcess() {
            console.log('in refactor parent process')
            this.isLoading = true

            var tempProcess = {}
            var properties = {}
            var tempObj = {}
            var registryID = ''
            var tempArray = []

            for (var i in this.parentProcessData) {
                properties = {}
                tempObj = {}
                registryID = this.parentProcessData[i].RegistryID
                tempProcess[registryID] = []

                for (var j in this.parentProcessData[i]) {
                    if (tempProcess.hasOwnProperty(registryID)) {
                        properties[j] = this.parentProcessData[i][j]
                        tempObj[j] = properties[j]
                    } else {
                        properties[j] = this.parentProcessData[i][j]
                        tempArray.push(properties[j])
                        tempProcess[registryID] = tempArray
                    }
                }
                tempProcess[registryID].push(tempObj)
            }

            this.parentProcessData = tempProcess
            this.isLoading = false
            console.log('out refactor parent process')
        },
        refactorChildProcess() {
            console.log('in refactor child process')
            this.isLoading = true

            var tempProcess = {}
            var properties = {}
            var tempObj = {}
            var registryID = ''
            var tempArray = []

            for (var i in this.childProcessData) {
                properties = {}
                registryID = this.childProcessData[i].RegistryID
                tempProcess[registryID] = []
                for (var j in this.childProcessData[i]) {
                    if (tempProcess.hasOwnProperty(registryID)) {
                        properties[j] = this.childProcessData[i][j]
                        tempObj[j] = properties[j]
                    } else {
                        properties[j] = this.childProcessData[i][j]
                        tempArray.push(properties[j])
                        tempProcess[registryID] = tempArray
                    }
                }
                tempProcess[registryID].push(tempObj)
            }

            this.childProcessData = tempProcess
            this.isLoading = false
            console.log('out refactor child process')
        },
        refactorParentRegistry() {
            this.isLoading = true

            var tempRegistry = {}
            var properties = {}
            var registryID = ''
            for (var i in this.parentRegistryData) {
                properties = {}
                registryID = this.parentRegistryData[i].RegistryID
                tempRegistry[registryID] = {}
                for (var j in this.parentRegistryData[i]) {
                    properties[j] = this.parentRegistryData[i][j]
                    tempRegistry[registryID][j] = properties[j]
                }
            }

            this.parentRegistryData = tempRegistry
            this.isLoading = false
        },
        refactorChildRegistry() {
            this.isLoading = true

            var tempRegistry = {}
            var properties = {}
          var registryID = ''
          for (var i in this.childRegistryData) {
              properties = {}
              registryID = this.childRegistryData[i].RegistryID
              tempRegistry[registryID] = {}
              for (var j in this.childRegistryData[i]) {
                  properties[j] = this.childRegistryData[i][j]
                  tempRegistry[registryID][j] = properties[j]
              }
          }

          this.childRegistryData = tempRegistry
          this.isLoading = false
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

          for (var i in this.parentProcessData) {
              for (var j in this.parentProcessData[i]){
                  chartMin.push(this.parentProcessData[i][j].ProcessBeginTime)
                  chartMax.push(this.parentProcessData[i][j].ProcessEndTime)
              }
          }

          this.chartOptions.hAxis.minValue = new Date(Math.min(...chartMin))
          this.chartOptions.hAxis.maxValue = new Date(Math.max(...chartMax))
      },
      resetFilters() {
        this.activeServerFilters = []
        this.activeItemTypeFilters = []
        this.activeDatabaseFilters = []
      },

      intersection() {
        var result = [];
        var lists;

        if(arguments.length === 1) {
          lists = arguments[0];
        } else {
          lists = arguments;
        }

        for(var i = 0; i < lists.length; i++) {
          var currentList = lists[i];
          for(var y = 0; y < currentList.length; y++) {
              var currentValue = currentList[y];
            if(result.indexOf(currentValue) === -1) {
              var existsInAll = true;
              for(var x = 0; x < lists.length; x++) {
                if(lists[x].indexOf(currentValue) === -1) {
                  existsInAll = false;
                  break;
                }
              }
              if(existsInAll) {
                result.push(currentValue);
              }
            }
          }
        }
        return result;
      },
      applyFilters() {
        this.chartData = []
        var registryList = []
        var itemTypeList = []
        var serverList = []
        var databaseList = []
        var allRegistryIDs = []

        for (var i in this.parentRegistryData) {
          allRegistryIDs.push(this.parentRegistryData[i].RegistryID)
        }

        for (var i in this.parentRegistryData) {
          for (var j in this.activeItemTypeFilters) {
            if (this.parentRegistryData[i].ObjectType == this.activeItemTypeFilters[j]) {
              itemTypeList.push(this.parentRegistryData[i].RegistryID)
            }
          }
        }
        for (var i in this.parentProcessData) {
          for (var j in this.activeServerFilters) {
            for (var k in this.parentProcessData[i]) {
              if (this.parentProcessData[i][k].ServerName == this.activeServerFilters[j]) {
                serverList.push(this.parentProcessData[i][k].RegistryID)
              }
            }
          }
        }
        for (var i in this.parentProcessData) {
          for (var j in this.activeDatabaseFilters) {
            for (var k in this.parentProcessData[i]) {
              if (this.parentProcessData[i][k].DatabaseName == this.activeDatabaseFilters[j]) {
                databaseList.push(this.parentProcessData[i][k].RegistryID)
              }
            }
          }
        }

        if (this.activeDatabaseFilters.length == 0) {
          databaseList = allRegistryIDs
        }
        if (this.activeServerFilters.length == 0) {
          serverList = allRegistryIDs
        }
        if (this.activeItemTypeFilters.length == 0) {
          itemTypeList = allRegistryIDs
        }
        console.log(databaseList)
        console.log(serverList)
        console.log(itemTypeList)
        registryList = this.intersection(databaseList, serverList, itemTypeList)

        console.log(registryList)
        for (var i in this.parentProcessData) {
          for (var j in this.parentProcessData[i]) {
            if (registryList.includes(this.parentProcessData[i][j].RegistryID)) {
              this.chartData.push([ this.parentProcessData[i][j].Process, String(this.parentProcessData[i][j].ProcessID), new Date(this.parentProcessData[i][j].ProcessBeginTime), new Date(this.parentProcessData[i][j].ProcessEndTime), String(this.parentProcessData[i][j].ProcessID) ])
            }
         }
        }

        console.log('chartdata')
        console.log(this.chartData)
        if (this.chartData.length > 0) {
          this.drawTimeline = true
        }
        setTimeout(this.drawDashboard(), 3000)
      },
      drawTasks(processLogID) {
          var container = document.getElementById('task-modal-dashboard');
          var chart = new google.visualization.Timeline(container);
          var dataTable = new google.visualization.arrayToDataTable(this.taskChartData);
          var options = {
              height: 100,
              timeline: {
                  showRowLabels: true
              },
              width: this.chartWidth
          };
          this.processText = "";
          for (var key in this.data[processLogID]) {
              if (key !== "Tasks") {
                  this.processText +=
                        (key === "ParentProcessLogID" ? "" : "<br>") +
                        "<span class=\"body-1 font-weight-bold\">" + key + ":</span> " +
                        "<span class=\"body-1\">" + (!this.data[processLogID][key] ? "" : this.data[processLogID][key]) + "</span>";
                }
            }
            chart.draw(dataTable, options);
        },
        getWindowHeight(event) {
            this.windowHeight = document.documentElement.clientHeight;
        },
        getWindowWidth(event) {
            this.windowWidth = document.documentElement.clientWidth;
            this.setChartWidth();
            if (this.modalVisible) {
                this.drawTasks();
            }
        },
        setChartWidth() {
            this.chartWidth = parseInt(document.defaultView.getComputedStyle(document.getElementById('task-modal-layout')).width, 10);
        },
        showModal(processLogID) {
            this.modalVisible = true;
            setTimeout(() => (this.drawTasks(processLogID)), 100);
        }
    },
    created: function() {

    }
}
</script>
<style>
    .headertile {
        background: #EEEEEE;
    }

    text {
        pointer-events: none;
    }
</style>
