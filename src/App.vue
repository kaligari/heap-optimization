<script setup lang="ts">
import { computed, nextTick, onMounted, reactive, ref } from 'vue'

interface IPoint {
    x: number
    y: number
}

interface IData {
    numbers: number[]
}

const data: IData = reactive({ numbers: Array.from({ length: 43 }, () => Math.floor(Math.random() * 50))})
const data2: IData = reactive({ numbers: [] })
const length = ref(data2.numbers.length)

const totalHeapLevels = computed(() => length.value ? Math.floor(Math.log2(length.value)) + 1 : 0)

const amountOfHeapCols = (level: number) => Math.pow(2, level - 1)
const totalAmountOfCols = computed(() => amountOfHeapCols(totalHeapLevels.value - 1))
const calcNodeIdx = (level: number, nodeInRow: number) => Math.pow(2, level - 1) + nodeInRow - 1 
const getNodeCoord = (idx: number, parent: boolean): IPoint | null => {
    const node = document.getElementById(`node-${idx}`)?.getBoundingClientRect()
    if(!node)  return null
    return {
        x: node.x + node.width / 2,
        y: node.y + (parent ? node.height : 0),
    }
    
}
const ready = ref(false)
const iteration = ref(0)

const compareWithParent = (current: number) => {
    const parent = Math.floor((current - 1) / 2)
    if(parent >= 0) {
        // console.log(parent);
        const parentVal = data.numbers[parent]
        const destVal = data.numbers[current]
        // console.log(parentVal, destVal, parentVal > destVal);
        if(parentVal > destVal) {
            data.numbers[parent] = destVal
            data.numbers[current] = parentVal
        }    
        compareWithParent(parent)
    }
}

const buttonHandler = () => {
    if(iteration.value > data.numbers.length - 1) return
    const current = iteration.value
    data2.numbers[current] = data.numbers[current]
    compareWithParent(current)
    iteration.value++
    length.value = data2.numbers.length
}

onMounted(() => {
    console.log('totalCheapLevels', totalHeapLevels.value);
    console.log('totalAmountOfCols', totalAmountOfCols.value);
    console.log('numOfCheapRows', amountOfHeapCols(totalHeapLevels.value));
    console.log('data', data);
    
    ready.value = true
})

</script>

<template>
    <div class="container">
        <div class="row" v-for="level in totalHeapLevels">
            <template
                v-for="nodeInRow in amountOfHeapCols(level)"
                v-bind:key="`node-${nodeInRow}`"
            >
                <div
                    v-if="calcNodeIdx(level, nodeInRow) <= length"
                    :id="`node-${calcNodeIdx(level, nodeInRow)}`"
                    class="node"
                >
                    {{ data.numbers[calcNodeIdx(level, nodeInRow) - 1] }}
                </div>
                <div v-else class="node hidden">&nbsp</div>
                <div
                    v-show="ready && level > 1 && calcNodeIdx(level, nodeInRow) <= length && getNodeCoord( calcNodeIdx(level, nodeInRow), false )"
                    class="lines"
                >
                    <svg width="100%" height="100%">
                        <line
                            :x1="getNodeCoord( calcNodeIdx(level, nodeInRow), false )?.x"
                            :y1="getNodeCoord( calcNodeIdx(level, nodeInRow), false )?.y"
                            :x2="getNodeCoord( Math.floor(calcNodeIdx(level, nodeInRow) / 2), true )?.x"
                            :y2="getNodeCoord( Math.floor(calcNodeIdx(level, nodeInRow) / 2), true )?.y"
                            stroke="black"
                        />
                    </svg>
                </div>
            </template>
        </div>
    </div>
    <button @click="buttonHandler" class="button">
        NEXT
    </button>
</template>

<style>
.container {
    position: relative;
    width: 100vw;
    height: 100vh;
}
.row {
    display: flex;
    justify-content: space-around;
}
.node {
    border: 1px solid #000;;
    display: block;
    width: 100px;
    height: 100px;
    margin: 10px;
    display: flex;
    justify-content: center;
    align-items: center;
    margin-bottom: 50px;
}
.hidden {
    visibility: hidden;
}
.lines,
.container {
    position: absolute;
    width: 100vw;
    height: 100vh;
    top: 0;
    left: 0;
}
.button {
    position: absolute;
    bottom: 0;
    right: 0;
    background: #F00;
    padding: 50px 100px
}
</style>