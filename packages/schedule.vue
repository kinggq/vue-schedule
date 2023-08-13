<script setup lang="ts">
import { ref, watch } from 'vue'

const tbodyRef = ref<HTMLElement>()
const weeks = ['星期一', '星期二', '星期三', '星期四', '星期五', '星期六', '星期日']

const isDragging = ref(false)
const left = ref<number>()
const top = ref<number>()
const startX = ref<number>()
const startY = ref<number>()
const width = ref(0)
const height = ref(0)

const tbodyTop = ref<number>(0)
const tbodyLeft = ref<number>(0)
const tbodyWidth = ref<number>(0)
const tbodyHeight = ref<number>(0)

function startDrag(event: MouseEvent) {
  if (tbodyRef.value) {
    const rect = tbodyRef.value.getBoundingClientRect()
    tbodyTop.value = rect.top + window.scrollY
    tbodyLeft.value = rect.left + window.scrollX + 80
    tbodyWidth.value = tbodyRef.value.clientWidth
    tbodyHeight.value = tbodyRef.value.clientHeight
    // const cell = tbodyHeight.value / 7
    // const row = tbodyWidth.value / 48
    // console.log('row', row)
    // console.log('top', top, ',left', left, ',tbodyWidth', tbodyWidth)
    if (handleBeyond(event.clientX, event.clientY)) {
      // console.log(((event.clientX - tbodyLeft.value) / row))
      startX.value = event.clientX
      startY.value = event.clientY
      left.value = event.clientX
      top.value = event.clientY
      isDragging.value = true
    }
    document.addEventListener('mouseup', stopDrag)
  }
}

function handleDrag(event: MouseEvent) {
  if (isDragging.value) {
    if (!handleBeyond(event.clientX, event.clientY))
      return

    width.value = Math.abs(event.clientX - startX.value!)
    height.value = Math.abs(event.clientY - startY.value!)
    if (event.clientX < startX.value!)
      left.value = event.clientX

    if (event.clientY < startY.value!)
      top.value = event.clientY
  }
}

function stopDrag() {
  isDragging.value = false

  document.removeEventListener('mouseup', stopDrag)
}

function getMaskPosition() {
  return `left: ${left.value}px; top:${top.value}px;width:${width.value}px;height:${height.value}px;`
}

function handleBeyond(x: number, y: number) {
  if (x > tbodyLeft.value && x < tbodyLeft.value + tbodyWidth.value && y > tbodyTop.value && y < tbodyHeight.value + tbodyTop.value)
    return true
  return false
}

watch(() => isDragging.value, (newValue) => {
  if (!newValue)
    resetStatus()
})

function resetStatus() {
  left.value = undefined
  top.value = undefined
  startX.value = undefined
  startY.value = undefined
  width.value = 0
  height.value = 0
}
</script>

<template>
  <pre>
    isDragging:{{ isDragging }}
  startX:{{ startX }}
  startY:{{ startY }}
  width: {{ width }}
  height: {{ height }}
  left: {{ left }}
  top: {{ top }}
  </pre>
  <div
    h-600px w-800px select-none border rounded-4px @mousedown="startDrag"
    @mousemove="handleDrag"
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
        <tbody ref="tbodyRef">
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
