<script setup lang="ts">
import { ref } from 'vue'

const weeks = ['星期一', '星期二', '星期三', '星期四', '星期五', '星期六', '星期日']

const isDragging = ref(false)
const startCell = ref<number>()
const endCell = ref<number>()
const left = ref<number>()
const top = ref<number>()
const clientX = ref<number>()
const clientY = ref<number>()
const width = ref(0)
const height = ref(0)

function startDrag(event: MouseEvent, cellIndex: number) {
  // console.log(event)
  clientX.value = event.clientX
  left.value = event.clientX
  top.value = event.clientY
  clientY.value = event.clientY
  isDragging.value = true
  startCell.value = cellIndex
  endCell.value = cellIndex
}

function handleDrag(event: MouseEvent, cellIndex: number) {
  if (isDragging.value) {
    width.value = Math.abs(event.clientX - clientX.value!)
    height.value = Math.abs(event.clientY - clientY.value!)
    endCell.value = cellIndex
    if (event.clientX - left.value! < 0)
      left.value = event.clientX

    if (event.clientY - top.value! < 0)
      top.value = event.clientY
  }
}

function stopDrag() {
  isDragging.value = false
}

function getMaskPosition() {
  return `left: ${left.value}px; top:${top.value}px;width:${width.value}px;height:${height.value}px;`
}
</script>

<template>
  isDragging:{{ isDragging }}
  startCell:{{ startCell }}
  endCell:{{ endCell }}
  clientX:{{ clientX }}
  clientY:{{ clientY }}
  <br>
  width: {{ width }}
  height: {{ height }}
  <div
    h-600px w-800px select-none border rounded-4px @mousedown="startDrag($event, 1)"
    @mousemove="handleDrag($event, 1)"
    @mouseup="stopDrag"
  >
    <div h-40px flex items-center px-20px border-b="1px">
      <div flex="~ gap3" text="12px">
        <div flex="~ gap2" items-center>
          <div h-6px w-20px rounded-2px bg-blue-500 />
          <div>
            已选
          </div>
        </div>
        <div flex="~ gap2" items-center>
          <div h-2px w-8px border="0.5px gray" />
          <div>未选</div>
        </div>
      </div>
    </div>
    <div>
      <table display-table h-full w-full border-spacing-0>
        <thead>
          <tr>
            <th rowspan="2" w-80px border-r="1px" border-b="1px">
              星期/时间
            </th>
            <th colspan="24" border-r="1px" border-b="1px">
              00:00 - 12:00
            </th>
            <th colspan="24" border-b="1px">
              12:00 - 24:00
            </th>
          </tr>
          <tr>
            <th
              v-for="item in 24"
              :key="item - 1"

              :border-r="item === 24 ? 'none' : '1px'"
              colspan="2"
              text="13px center"
              min-w-12px border-b
            >
              {{ item - 1 }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(week, index) in weeks"
            :key="index"
            border-b
          >
            <td border-r>
              {{ week }}
            </td>
            <td
              v-for="x in 48"
              :key="x"
              :border-r="x === 48 ? 'none' : '1px'"
              colspan="1"
            />
          </tr>
        </tbody>
      </table>
      <div
        fixed
        bg="blue-500/10"
        :style="getMaskPosition()"
      />
    </div>
  </div>
</template>

<style>
table {
  table-layout: fixed;
}
</style>
