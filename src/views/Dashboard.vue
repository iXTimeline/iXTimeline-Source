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
import parentRegistryData from '../assets/ParentRegistry.json'
import childRegistryData from '../assets/ChildRegistry.json'
// import parentProcessData from '../assets/ParentProcessLog.json'
import childProcessData from '../assets/ChildProcessLog.json'
import taskLogData from '../assets/TaskLogs.json'
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
            taskLogData: taskLogData,
            chartOptions: {
                hAxis: {
                    minValue: new Date('2019-06-01'),
                    maxValue: new Date('2019-06-02')
                }
            },
            processLogID: 0,
            registryData: {},
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
            console.log(resp.data);
            this.isLoading = true
            this.refactor();

            this.chartData.push([
              {id: 'Process', label: 'Process', type: 'string'},
              {id: 'Task', label: 'Task', type: 'string'},
              {id: 'Start', label: 'Start', type: 'date'},
              {id: 'End', label: 'End', type: 'date'},
              {id: 'Tooltip', role: 'tooltip', type: 'string'}
            ]);

            for (var parentRegistry in this.parentRegistryData) {
                var registry = this.parentRegistryData[parentRegistry].Registry;

                for (var i in this.parentRegistryData[parentRegistry].ChildRegistryIDs) {
                    var childRegistryID = this.parentRegistryData[parentRegistry].ChildRegistryIDs[i];
                    var label = registry + "|" + this.childRegistryData[childRegistryID].Registry;

                    for (var j in this.childRegistryData[childRegistryID].ProcessLogIDs) {
                        var processLogID = this.childRegistryData[childRegistryID].ProcessLogIDs[j];
                        var process = this.childProcessData[processLogID].Process;
                        var start = new Date(this.childProcessData[processLogID].ProcessBeginTime);
                        var end = new Date(this.childProcessData[processLogID].ProcessEndTime);

                        this.chartData.push([label, process, start, end, processLogID]);
                    }
                }

                for (var k in this.parentRegistryData[parentRegistry].ProcessLogIDs) {
                    var processLogID = this.parentRegistryData[parentRegistry].ProcessLogIDs[k];
                    var process = this.parentProcessData[processLogID].Process;
                    var start = new Date(this.parentProcessData[processLogID].ProcessBeginTime);
                    var end = new Date(this.parentProcessData[processLogID].ProcessEndTime);

                    this.chartData.push([registry, process, start, end, processLogID]);
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
        drawDashboard() {console.log("draw");
            var chart = new google.visualization.Timeline(document.getElementById('chart'));
            var data = new google.visualization.arrayToDataTable(this.chartData);
            var vm = this;

            var options = {
                tooltip: {
                    trigger: "none"
                }
            };

            google.visualization.events.addListener(chart, 'select', function() {
                var selection = chart.getSelection();

                if (selection.length > 0) {
                    vm.showModal(data.getValue(selection[0].row, 4));
                }
            });
          //dashboard.bind(control, chart);
            console.log(this.chartData);
              if (this.chartData.length > 1) {
                chart.draw(data, options);
              }
              else {
                  alert("0 records found.");
              }
        },
        refactor() {console.log("ref");
            var parentRegistryJson = {};
            var childRegistryJson = {};
            var parentProcessJson = {};
            var childProcessJson = {};
            var taskLogJson = {};

            for (var i in this.parentRegistryData) {
                var tempJson = {};
                var registryID = this.parentRegistryData[i]["RegistryID"];

                for (var key in this.parentRegistryData[i]) {
                    if (key !== "RegistryID" && key !== "ParentRegistryID") {
                        tempJson[key] = this.parentRegistryData[i][key];
                    }
                }

                tempJson["ProcessLogIDs"] = [];
                tempJson["ChildRegistryIDs"] = [];
                parentRegistryJson[registryID] = tempJson;
            }

            for (var i in this.childRegistryData) {
                var tempJson = {};
                var registryID = this.childRegistryData[i]["RegistryID"];
                var parentRegistryID = this.childRegistryData[i]["ParentRegistryID"];

                for (var key in this.childRegistryData[i]) {
                    if (key !== "RegistryID") {
                        tempJson[key] = this.childRegistryData[i][key];
                    }
                }

                tempJson["ProcessLogIDs"] = [];
                childRegistryJson[registryID] = tempJson;
                parentRegistryJson[parentRegistryID].ChildRegistryIDs.push(registryID);
            }

            for (var i in this.parentProcessData) {
                var tempJson = {};
                var processLogID = this.parentProcessData[i]["ProcessLogID"];
                var registryID = this.parentProcessData[i]["RegistryID"];

                for (var key in this.parentProcessData[i]) {
                    if (key !== "ProcessLogID" && key !== "ParentProcessLogID") {
                        tempJson[key] = this.parentProcessData[i][key];
                    }
                }

                tempJson["ChildProcessLogIDs"] = [];
                parentProcessJson[processLogID] = tempJson;
                parentRegistryJson[registryID].ProcessLogIDs.push(processLogID);
            }

            for (var i in this.childProcessData) {
                var tempJson = {};
                var processLogID = this.childProcessData[i]["ProcessLogID"];
                var parentProcessLogID = this.childProcessData[i]["ParentProcessLogID"];
                var registryID = this.childProcessData[i]["RegistryID"];

                for (var key in this.childProcessData[i]) {
                    if (key !== "ProcessLogID") {
                        tempJson[key] = this.childProcessData[i][key];
                    }
                }

                childProcessJson[processLogID] = tempJson;
                parentProcessJson[parentProcessLogID].ChildProcessLogIDs.push(processLogID);
                childRegistryJson[registryID].ProcessLogIDs.push(processLogID);
            }

            for (var i in this.taskLogData) {
                var tempJson = {};
                var taskLogID = this.taskLogData[i]["TaskLogID"];
                var processLogID = this.taskLogData[i]["ProcessLogID"];

                for (var key in this.taskLogData[i]) {
                    if (!key.match(/Measure.*/) && key !== "TaskLogID") {
                        tempJson[key] = this.taskLogData[i][key];
                    }
                }

                if (taskLogJson.hasOwnProperty(processLogID)) {
                    taskLogJson[processLogID].push(tempJson);
                }
                else {
                    taskLogJson[processLogID]= [];
                    taskLogJson[processLogID].push(tempJson);
                }
            }

            this.parentRegistryData = parentRegistryJson;
            this.childRegistryData = childRegistryJson;
            this.parentProcessData = parentProcessJson;
            this.childProcessData = childProcessJson;
            this.taskLogData = taskLogJson;console.log("ref ed");
        },
        // refactorParentProcess() {
        //     // console.log('in refactor parent process')
        //     this.isLoading = true
        //
        //     var tempProcess = {}
        //     var properties = {}
        //     var tempObj = {}
        //     var registryID = ''
        //     var tempArray = []
        //
        //     for (var i in this.parentProcessData) {
        //         properties = {}
        //         tempObj = {}
        //         registryID = this.parentProcessData[i].RegistryID
        //         tempProcess[registryID] = []
        //
        //         for (var j in this.parentProcessData[i]) {
        //             if (tempProcess.hasOwnProperty(registryID)) {
        //                 properties[j] = this.parentProcessData[i][j]
        //                 tempObj[j] = properties[j]
        //             } else {
        //                 properties[j] = this.parentProcessData[i][j]
        //                 tempArray.push(properties[j])
        //                 tempProcess[registryID] = tempArray
        //             }
        //         }
        //         tempProcess[registryID].push(tempObj)
        //     }
        //
        //     this.parentProcessData = tempProcess
        //     this.isLoading = false
        //     // console.log('out refactor parent process')
        // },
        // refactorChildProcess() {
        //     // console.log('in refactor child process')
        //     this.isLoading = true
        //
        //     var tempProcess = {}
        //     var properties = {}
        //     var tempObj = {}
        //     var registryID = ''
        //     var tempArray = []
        //
        //     for (var i in this.childProcessData) {
        //         properties = {}
        //         registryID = this.childProcessData[i].RegistryID
        //         tempProcess[registryID] = []
        //         for (var j in this.childProcessData[i]) {
        //             if (tempProcess.hasOwnProperty(registryID)) {
        //                 properties[j] = this.childProcessData[i][j]
        //                 tempObj[j] = properties[j]
        //             } else {
        //                 properties[j] = this.childProcessData[i][j]
        //                 tempArray.push(properties[j])
        //                 tempProcess[registryID] = tempArray
        //             }
        //         }
        //         tempProcess[registryID].push(tempObj)
        //     }
        //
        //     this.childProcessData = tempProcess
        //     this.isLoading = false
        //     // console.log('out refactor child process')
        // },
        // refactorParentRegistry() {
        //     this.isLoading = true
        //
        //     var tempRegistry = {}
        //     var properties = {}
        //     var registryID = ''
        //     for (var i in this.parentRegistryData) {
        //         properties = {}
        //         registryID = this.parentRegistryData[i].RegistryID
        //         tempRegistry[registryID] = {}
        //         for (var j in this.parentRegistryData[i]) {
        //             properties[j] = this.parentRegistryData[i][j]
        //             tempRegistry[registryID][j] = properties[j]
        //         }
        //     }
        //
        //     this.parentRegistryData = tempRegistry
        //     this.isLoading = false
        // },
        // refactorChildRegistry() {
        //     this.isLoading = true
        //
        //     var tempRegistry = {}
        //     var properties = {}
        //     var registryID = ''
        //     for (var i in this.childRegistryData) {
        //       properties = {}
        //       registryID = this.childRegistryData[i].RegistryID
        //       tempRegistry[registryID] = {}
        //       for (var j in this.childRegistryData[i]) {
        //           properties[j] = this.childRegistryData[i][j]
        //           tempRegistry[registryID][j] = properties[j]
        //       }
        //     }
        //
        //       maxProcessEndArray = []
        //       minProcessBeginArray = []
        //   }
      // },
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

        this.chartData.push([
          {id: 'Process', label: 'Process', type: 'string'},
          {id: 'Task', label: 'Task', type: 'string'},
          {id: 'Start', label: 'Start', type: 'date'},
          {id: 'End', label: 'End', type: 'date'},
          {id: 'Tooltip', role: 'tooltip', type: 'string'}
        ]);

        for (var i in this.parentRegistryData) {
          allRegistryIDs.push(i)
        }

        for (var i in this.parentRegistryData) {
          for (var j in this.activeItemTypeFilters) {
            if (this.parentRegistryData[i].ObjectType == this.activeItemTypeFilters[j]) {
              itemTypeList.push(i)
            }
          }
        }
        for (var i in this.parentProcessData) {
          for (var j in this.activeServerFilters) {
            for (var k in this.parentProcessData[i]) {
              if (this.parentProcessData[i].ServerName == this.activeServerFilters[j]) {
                serverList.push(this.parentProcessData[i].RegistryID.toString())
              }
            }
          }
        }
        for (var i in this.parentProcessData) {
          for (var j in this.activeDatabaseFilters) {
            for (var k in this.parentProcessData[i]) {
              if (this.parentProcessData[i].DatabaseName == this.activeDatabaseFilters[j]) {
                databaseList.push(this.parentProcessData[i].RegistryID.toString())
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
        // for (var i in this.parentProcessData) {
        //   for (var j in this.parentProcessData[i]) {
        //     if (registryList.includes(this.parentProcessData[i][j].RegistryID)) {
        //       this.chartData.push([ this.parentProcessData[i][j].Process, String(this.parentProcessData[i][j].ProcessID), new Date(this.parentProcessData[i][j].ProcessBeginTime), new Date(this.parentProcessData[i][j].ProcessEndTime), String(this.parentProcessData[i][j].ProcessID) ])
        //     }
        //  }
        // }

        for (var p in registryList) {
            var registryID = registryList[p];
            var registry = this.parentRegistryData[registryID].Registry;

            for (var i in this.parentRegistryData[registryID].ChildRegistryIDs) {
                var childRegistryID = this.parentRegistryData[registryID].ChildRegistryIDs[i];
                var label = registry + "|" + this.childRegistryData[childRegistryID].Registry;

                console.log("asdf " + childRegistryID);
                for (var j in this.childRegistryData[childRegistryID].ProcessLogIDs) {
                    var processLogID = this.childRegistryData[childRegistryID].ProcessLogIDs[j];
                    var process = this.childProcessData[processLogID].Process;
                    var start = new Date(this.childProcessData[processLogID].ProcessBeginTime);
                    var end = new Date(this.childProcessData[processLogID].ProcessEndTime);

                    this.chartData.push([label, process, start, end, processLogID]);
                }
            }

            for (var k in this.parentRegistryData[registryID].ProcessLogIDs) {
                var processLogID = this.parentRegistryData[registryID].ProcessLogIDs[k];
                var process = this.parentProcessData[processLogID].Process;
                var start = new Date(this.parentProcessData[processLogID].ProcessBeginTime);
                var end = new Date(this.parentProcessData[processLogID].ProcessEndTime);

                this.chartData.push([registry, process, start, end, processLogID]);
            }
        }

        console.log(this.chartData)
        if (this.chartData.length > 1) {
          this.drawTimeline = true
        }
        setTimeout(this.drawDashboard(), 3000)
      },
      drawTasks(processLogID) {
            var container = document.getElementById('task-modal-dashboard');
            var chart = new google.visualization.Timeline(container);
            var modalData = [];
            var options = {
              height: 100,
              timeline: {
                  showRowLabels: true
              },
              width: this.chartWidth
            };

            modalData.push([
              {id: 'Process', label: 'Process', type: 'string'},
              {id: 'Task', label: 'Task', type: 'string'},
              {id: 'Start', label: 'Start', type: 'date'},
              {id: 'End', label: 'End', type: 'date'}
            ]);

            this.processText = "";

            for (var key in this.taskLogData[processLogID][0]) {
                if (key !== "TaskLogID") {
                  this.processText +=
                        (key === "ProcessLogID" ? "" : "<br>") +
                        "<span class=\"body-1 font-weight-bold\">" + key + ":</span> " +
                        "<span class=\"body-1\">" + (!this.taskLogData[processLogID][0][key] ? "" : this.taskLogData[processLogID][0][key]) + "</span>";
                }
            }

            for (var i in this.taskLogData[processLogID]) {
                var processLogID = this.taskLogData[processLogID][i]["ProcessLogID"];
                var process = "";
                var task = this.taskLogData[processLogID][i]["TaskType"];
                var start = new Date(this.taskLogData[processLogID][i]["BeginTime"]);
                var end = new Date(this.taskLogData[processLogID][i]["EndTime"]);

                if (this.parentProcessData.hasOwnProperty(processLogID)) {
                    process = this.parentProcessData[processLogID].Process;
                }

                if (this.childProcessData.hasOwnProperty(processLogID)) {
                    process = this.childProcessData[processLogID].Process;
                }

                modalData.push([process, task, start, end]);
            }

            var data = new google.visualization.arrayToDataTable(modalData);
            chart.draw(data, options);
        },
        getWindowHeight(event) {
            this.windowHeight = document.documentElement.clientHeight;
        },
        getWindowWidth(event) {
            this.windowWidth = document.documentElement.clientWidth;
            this.setChartWidth();
            if (this.modalVisible) {
                this.drawTasks(this.processLogID);
            }
        },
        setChartWidth() {
            this.chartWidth = parseInt(document.defaultView.getComputedStyle(document.getElementById('task-modal-layout')).width, 10);
        },
        showModal(processLogID) {
            this.modalVisible = true;
            this.processLogID = processLogID;
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
