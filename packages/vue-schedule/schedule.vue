<script setup lang="ts">
import { computed, ref, watch } from 'vue'

const props = withDefaults(defineProps<Props>(), {
  selectedText: '已选',
  unselectedText: '未选',
  morningText: '00:00 - 12:00',
  afternoonText: '12:00 - 24:00',
  weeksText: () => ['星期一', '星期二', '星期三', '星期四', '星期五', '星期六', '星期日'],
  placeholder: '可拖动鼠标选择时间段',
})
const emits = defineEmits<{
  (e: 'update:value', value: string): void
  (e: 'formatTime', list: FormatTime): void
}>()

defineOptions({
  name: 'VueSchedule',
})

interface Props {
  value: string
  selectedText?: string
  unselectedText?: string
  morningText?: string
  afternoonText?: string
  weeksText?: string[]
  placeholder?: string
}

interface State {
  selected: boolean
  x: number
  y: number
}

interface FormatTime {
  [key: string]: Array<string>
}

const value = computed({
  get() {
    return props.value
  },
  set(newValue) {
    emits('update:value', newValue)
  },
})

const tbodyRef = ref<HTMLElement>()
const isDragging = ref(false)
const startX = ref<number>()
const startY = ref<number>()
const moveX = ref<number>()
const moveY = ref<number>()

const minX = ref(0)
const maxX = ref(0)
const minY = ref(0)
const maxY = ref(0)

const td = ref(Array.from({ length: 7 }, (_, y) =>
  Array.from({ length: 48 }, (_, x): State => ({
    selected: false,
    x,
    y,
  })),
))

watch(() => props.value, () => {
  if (value.value.length !== 336) {
    console.warn('value类型错误，value必须是0，1组成的336位字符串')
    return
  }
  for (let s = 0; s < value.value.length; s++) {
    if (value.value[s] === '0')
      td.value.flat()[s].selected = false

    else if (value.value[s] === '1')
      td.value.flat()[s].selected = true
  }
})

const getDates = computed(() => {
  const dates = {} as any
  const weeks = props.weeksText
  td.value.forEach((nums, index) => {
    nums.forEach((item, i) => {
      if (item.selected) {
        if (!dates[weeks[index]])
          dates[weeks[index]] = []
        dates[weeks[index]].push(i)
      }
    })
    if (dates[weeks[index]])
      dates[weeks[index]] = formatTimeRanges(dates[weeks[index]])
  })
  return dates
})

watch(() => td.value, () => {
  emits('update:value', selectedToString())
  emits('formatTime', getDates.value)
}, { deep: true })

function selectedToString() {
  let str = ''
  td.value.flat().forEach((item) => {
    if (item.selected)
      str += '1'
    else
      str += '0'
  })
  return str
}

function startDrag(x: number, y: number) {
  isDragging.value = true
  startX.value = x
  startY.value = y
  document.addEventListener('mouseup', stopDrag)
}

function handleDrag(x: number, y: number) {
  if (isDragging.value) {
    moveX.value = x
    moveY.value = y
  }
}

function stopDrag() {
  isDragging.value = false
  let allSelected = false
  if (minY.value === maxY.value && minX.value === maxX.value) {
    resetState()
    return
  }
  for (let y = minY.value!; y <= maxY.value!; y++) {
    for (let x = minX.value!; x <= maxX.value!; x++) {
      if (!td.value[y][x].selected) {
        allSelected = true
        break
      }
    }
  }
  if (allSelected) {
    for (let y = minY.value!; y <= maxY.value!; y++) {
      for (let x = minX.value!; x <= maxX.value!; x++)
        td.value[y][x].selected = true
    }
  }
  else {
    for (let y = minY.value!; y <= maxY.value!; y++) {
      for (let x = minX.value!; x <= maxX.value!; x++)
        td.value[y][x].selected = false
    }
  }
  resetState()
}

function isPointBetween(x: number, y: number) {
  minX.value = Math.min(startX.value!, moveX.value!)
  maxX.value = Math.max(startX.value!, moveX.value!)
  minY.value = Math.min(startY.value!, moveY.value!)
  maxY.value = Math.max(startY.value!, moveY.value!)
  return x >= minX.value && x <= maxX.value && y >= minY.value && y <= maxY.value
}

function formatTime(num: number): string {
  const hours = Math.floor(num / 2).toString().padStart(2, '0')
  const minutes = (num % 2 === 0) ? '00' : '30'
  return `${hours}:${minutes}`
}

function formatTimeRanges(nums: number[]): string[] {
  if (!nums.length)
    return []

  nums.sort((a, b) => a - b)
  const result: string[] = []

  let start = nums[0]
  for (let i = 1; i < nums.length; i++) {
    if (nums[i] - nums[i - 1] !== 1) {
      const end = nums[i - 1]
      result.push(`${formatTime(start)} - ${formatTime(end + 1)}`)
      start = nums[i]
    }
  }
  result.push(`${formatTime(start)} - ${formatTime(nums[nums.length - 1] + 1)}`)

  return result
}

function resetState() {
  startX.value = undefined
  startY.value = undefined
  moveX.value = undefined
  moveY.value = undefined
  document.removeEventListener('mouseup', stopDrag)
}

function handleClear() {
  td.value.flat().forEach((item) => {
    item.selected = false
  })
}

const isEmpty = computed(() =>
  !td.value.flat().some(item => item.selected === true),
)
</script>

<template>
  <div
    bg="dark:gray/10"
    w-800px select-none border="1px dark:gray/5" rounded-4px
  >
    <div h-40px flex items-center px-20px border-b="1px dark:gray/10">
      <div flex="~ gap3" text="12px">
        <div flex="~ gap2" items-center>
          <div h-6px w-20px rounded-2px bg-blue-500 />
          <div>
            {{ selectedText }}
          </div>
        </div>
        <div flex="~ gap2" items-center>
          <div h-6px w-20px border="0.5px dark:gray/50" rounded-2px />
          <div>{{ unselectedText }}</div>
        </div>
      </div>
    </div>
    <div>
      <table h-full w-full border-spacing-0 table-fixed>
        <thead>
          <tr text="14px">
            <th
              rowspan="2"
              w-80px
              border-r="1px dark:gray/10"
              border-b="1px dark:gray/10"
              font-500
            >
              星期/时间
            </th>
            <th colspan="24" border-r="1px dark:gray/10" border-b="1px dark:gray/10">
              {{ morningText }}
            </th>
            <th colspan="24" border-b="1px dark:gray/10">
              {{ afternoonText }}
            </th>
          </tr>
          <tr>
            <th
              v-for="item in 24"
              :key="item - 1"

              :border-r="item === 24 ? 'none' : '1px dark:gray/10'"
              colspan="2"
              text="13px center"
              min-w-12px border-b="1px dark:gray/10"
            >
              {{ item - 1 }}
            </th>
          </tr>
        </thead>
        <tbody ref="tbodyRef">
          <tr
            v-for="(_, y) in td"
            :key="y"
            border-b="1px dark:gray/10"
          >
            <td border-r="1px dark:gray/10" py-4px text="center 14px">
              {{ weeksText[y] }}
            </td>
            <td
              v-for="item in td[y]"
              :key="item.x"
              :border-r="item.x === 47 ? 'none' : '1px dark:gray/10'"
              colspan="1"
              transition="all 0.1s ease"
              :bg=" isPointBetween(item.x, y) && item.selected ? 'blue-500/50 ' : isPointBetween(item.x, y) ? `blue-500/10` : item.selected ? 'blue dark:blue-500/60' : 'hover:gray-500/10'"
              @mousedown="startDrag(item.x, y)"
              @mousemove="handleDrag(item.x, y)"
              @click.prevent="item.selected = !item.selected"
            />
          </tr>
          <tr>
            <td colspan="49" p-20px text="14px">
              <div v-if="isEmpty" text="center">
                {{ placeholder }}
              </div>
              <div v-else>
                <div flex justify-between>
                  <div>已选时间段</div>
                  <div text="blue-500 hover:blue-500/80" cursor-pointer @click="handleClear">
                    清空
                  </div>
                </div>
                <div pt-20px>
                  <div
                    v-for="(item, index) in Object.keys(getDates)"
                    :key="index"
                    flex="~ gap5"
                  >
                    <div min-w-50px>
                      {{ item }}
                    </div>
                    <div>
                      {{ getDates[item].join('、') }}
                    </div>
                  </div>
                </div>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>
