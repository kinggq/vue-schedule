<h1>Schedule-Vue</h1>

## Install

```bash
npm i schedule-vue@latest
```
## Usage

```js
import VueSchedule from 'schedule-vue'
import 'schedule-vue/dist/style.css'

const value = ref('')
```
```html
<VueSchedule v-model:value="value" @format-time="times => time = times" />
```

```js
interface Props {
  value: string
  selectedText?: string
  unselectedText?: string
  morningText?: string
  afternoonText?: string
  weeksText?: string[]
  placeholder?: string
}

interface Emits {
  (e: 'update:value', value: string): void
  (e: 'formatTime', list: FormatTime): void
}

interface FormatTime {
  [key: string]: string[]
}
```

## Preview

![](https://github.com/kinggq/images/blob/main/1692010741569.jpg?raw=true)

![](https://github.com/kinggq/images/blob/main/1692010768836.jpg?raw=true)
