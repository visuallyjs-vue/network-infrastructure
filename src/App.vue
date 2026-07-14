<script setup>
import {
    DiagramProvider,
    DiagramComponent,
    DiagramPaletteComponent,
    ControlsComponent, PieChartComponent, BarChartComponent
} from "@visuallyjs/browser-ui-vue"
import { Group, FlowchartBasicEdgeMappings } from "@visuallyjs/browser-ui"
import { INFRASTRUCTURE_SHAPES } from "./infrastructure-shapes.js"
import { GRID_SIZE } from "./constants.js";
import CATALOG from "../catalog.json"
import InfrastructureInspector from "./Inspector.vue";
import Total from "./Total.vue";

const props = defineProps(['url'])

const diagramOptions = {
    shapes: [INFRASTRUCTURE_SHAPES],
    grid: {
        size: GRID_SIZE
    },
    cells:{
        rotatable:false,
        showLabels: false,
        groups:{
            elastic:true,
            padding:20
        },
        shouldDeleteGroupMembers:() => true
    },
    edges:{
        allowUnattached:false,
        propertyMappings:FlowchartBasicEdgeMappings(),
        deleteButton:"hover",
        showLabels:true
    },
    lasso:true,
    zoomToFit: true,
    mediator:{
        canResize:(obj) => obj.objectType === "Group",
        canDrop:(obj, target) => {
            return target == null || !(obj.objectType === Group.objectType && target.objectType === Group.objectType)
        }
    }
}

const chartFontSpec = {
    size: 12
}

</script>

<template>
    <DiagramProvider>
        <div class="vjs-network-infrastructure">
            <div class="vjs-ni-left-sidebar">
                <h3>Palette</h3>
                <div class="vjs-ni-palette-section">
                    <DiagramPaletteComponent :showLabels="true" :iconSize="{width:40, height:40}" :dragSize="{width:80, height:80}" class="vjs-ni-palette" />
                </div>
                <h3>Properties</h3>
                <InfrastructureInspector/>
            </div>
            <div class="vjs-ni-diagram-container">
                <DiagramComponent :url="url" :options="diagramOptions" class="vjs-ni-vjs-surface">
                    <ControlsComponent class="vjs-ni-controls" />
                </DiagramComponent>
            </div>
            <div class="vjs-ni-sidebar">
                <div class="vjs-ni-chart-section">
                    <h3>Resource Metrics</h3>
                    <div class="vjs-ni-chart-card">
                        <h4>Monthly Spend</h4>
                        <Total/>
                        <PieChartComponent :options="{
                            colorGenerator: {
                                generate: (point) => CATALOG[point.data.id].color
                            },
                            labelFont: chartFontSpec,
                            dataLabels:true,
                            series:[
                                {
                                    type:'summing-collation',
                                    categoryField:'type',
                                    sumField:'monthlyPrice'
                                }
                            ]
                        }" :useModel="true"/>
                    </div>
                    <div class="vjs-ni-chart-card">
                        <h4>Instance Types</h4>
                        <BarChartComponent :options="{
                            categoryAxis: {
                                font: chartFontSpec
                            },
                            dataLabels:true,
                            series:[
                                {
                                    type:'collation',
                                    valueField:'type',
                                    color:'#445566'
                                }
                            ]
                        }" :useModel="true"/>
                    </div>
                </div>
            </div>
        </div>
    </DiagramProvider>
</template>
