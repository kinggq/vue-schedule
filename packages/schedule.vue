<script setup lang="ts">
const weeks = ['星期一', '星期二', '星期三', '星期四', '星期五', '星期六', '星期日']
</script>

<template>
  <div h-600px w-800px border rounded-4px>
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
              text="14px center"
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
    </div>
  </div>
</template>

<style>
table {
  display: table;
}
th,td{
  display: table-cell;
  vertical-align: inherit;
}
</style>
