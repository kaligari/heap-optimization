<script setup lang="ts">
import { computed, ref } from 'vue'
interface IChartNode {
    level: number
    colsInRow: number
    nodeInRow: number
    maxCols: number
    totalAmountOfCols: number
    totalAmountOfLevels: number
}
const props = defineProps<IChartNode>()
const totalWidth = ref(1000)
const totalHeight = ref(800)
const width = ref(totalWidth.value / (props.totalAmountOfCols + 1))
const height = ref(totalHeight.value / (props.totalAmountOfLevels + 1))
// const height = ref(150)
const posX = computed(() => (totalWidth.value / (props.colsInRow + 1)  * props.nodeInRow) - width.value / 2)
const posY = computed(() => (totalHeight.value / (props.totalAmountOfLevels + 1)  * props.level) - height.value / 2)

</script>
<template>
    <rect :x="posX" :y="posY" rx="5" ry="5" :width="width/2" :height="height/2"
  style="fill:red;stroke:black;stroke-width:2;opacity:0.5" />
    <text :x="posX" :y="posY">
        {{ props.totalAmountOfCols }}
    </text>
</template>