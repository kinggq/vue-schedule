<script setup lang="ts">
import { ref, watchEffect } from 'vue'
import VueSchedule from '../packages'
import PackageJson from '../package.json'

const value = ref('')

const time = ref<any>()

const dark = ref(false)

watchEffect(() => {
  const el = document.querySelector('html')
  el?.classList.toggle('dark', dark.value)
})
function toggleDark() {
  dark.value = !dark.value
}
</script>

<template>
  <main p="x-4 y-10">
    <div text-blue-500>
      <span text="30px" font-bold>
        Schedule Vue
      </span>
      <span text-orange>v{{ PackageJson.version }}</span>
    </div>
    <div mb-20px text-center>
      <nav mt-6 inline-flex gap-2 text-xl>
        <button icon-btn @click="toggleDark()">
          <div i-carbon-sun dark:i-carbon-moon />
        </button>

        <a
          i-carbon-logo-github icon-btn
          rel="noreferrer"
          href="https://github.com/kinggq/vue-schedule"
          target="_blank"
          title="GitHub"
        />
      </nav>
    </div>
    <VueSchedule v-model:value="value" @format-time="times => time = times" />

    <div mt-30px w-full overflow-hidden>
      <div text="14px amber-700/90" w-full break-words>
        value: '{{ value }}'
      </div>
      <pre>
        格式化后的时间: {{ time }}
      </pre>
    </div>
    <div py-20px text-20px font-bold>
      usage
    </div>
    <pre bg="gray/10" rounded-4px px-20px py-5px>npm i schedule-vue@latest</pre>
    <pre bg="gray/10" mt-10px overflow-auto rounded-4px px-20px py-5px>
import VueSchedule from 'schedule-vue'
import 'schedule-vue/dist/style.css'

const value = ref('')

&lt;VueSchedule v-model:value="value" @format-time="times => time = times" /&gt;
    </pre>
    <div py-20px text-20px font-bold>
      Props
    </div>
    <pre>
interface Props {
  value: string
  width?: string
  selectedText?: string
  unselectedText?: string
  morningText?: string
  afternoonText?: string
  weeksText?: string[]
  placeholder?: string
}
    </pre>
    <div py-20px text-20px font-bold>
      Emits
    </div>
    <pre>
interface Emits {
  (e: 'update:value', value: string): void
  (e: 'formatTime', list: FormatTime): void
}
    </pre>

    <pre>
interface FormatTime {
  [key: string]: string[]
}
</pre>
  </main>
</template>
