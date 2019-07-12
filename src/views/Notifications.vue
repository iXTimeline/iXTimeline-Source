<template>
    <v-container fluid grid-list-xl fill-height>
        <v-layout row justify-center align-center>
            <v-dialog v-model="modalVisible">
                <template v-slot:activator="{ on }">
                    <v-btn color="primary" dark v-on="on" @click="showModal">Open Dialog</v-btn>
                </template>
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
                                        <v-card-text></v-card-text>
                                    </v-card>
                                </v-flex>
                            </v-layout>
                        </v-container>
                </v-card>
            </v-dialog>
        </v-layout>
    </v-container>
</template>

<script>
import data from '../assets/processtest.json'

export default {
    data() {
        return {
            chartWidth: 0,
            data: data,
            modalVisible: false,
            processText: "",
            taskChartData: [],
            taskText: "",
            windowHeight: 0,
            windowWidth: 0
        }
    },
    mounted() {
        this.$nextTick(function() {
            window.addEventListener('resize', this.getWindowWidth);
            window.addEventListener('resize', this.getWindowHeight);

            //Init
            this.getWindowWidth();
            this.getWindowHeight();
        });

        document.onreadystatechange = () => {
            if (document.readyState == "complete") {
            }
        };

        this.taskChartData.push([
            {id: 'Process', label: 'Process', type: 'string'},
            {id: 'Task', label: 'Task', type: 'string'},
            {id: 'Start', label: 'Start', type: 'date'},
            {id: 'End', label: 'End', type: 'date'}
        ]);

        for (var process in this.data) {
            for (var task in this.data[process].Tasks) {
                this.taskChartData.push([
                    this.data[process].Process,
                    this.data[process].Tasks[task].TaskType,
                    new Date(this.data[process].Tasks[task].BeginTime),
                    new Date(this.data[process].Tasks[task].EndTime)]);
            }
        }
    },
    methods: {
        drawTasks() {
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

            for (var process in this.data) {
                for (var key in this.data[process]) {
                    if (key !== "Tasks") {
                        this.processText +=
                            (key === "ParentProcessLogID" ? "" : "<br>") +
                            "<span class=\"body-1 font-weight-bold\">" + key + ":</span> " +
                            "<span class=\"body-1\">" + (!this.data[process][key] ? "" : this.data[process][key]) + "</span>";
                    }
                }

                for (var task in this.data[process].Tasks) {
                }
            }

            google.visualization.events.addListener(chart, 'select', function() {
                console.log(dataTablechart.getSelection());
            });

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
        showModal() {
            this.modalVisible = true;

            setTimeout(() => (this.drawTasks()), 100);
        }
    }
}
</script>
