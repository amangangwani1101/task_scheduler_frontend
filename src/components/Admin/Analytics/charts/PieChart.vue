<template>
  <div id="user-stats">
    <h1 class="text-2xl font-semibold text-slate-900">{{ user.name }}'s Statistics</h1>
    <div class="mt-3 space-x-3">
      <select
        class="border-none focus:[box-shadow:none] bg-black rounded-md shadow font-medium text-white"
        v-model="statistics"
      >
        <option value="completed_vs_pending_tasks" selected>Completed vs Pending Tasks</option>
        <option value="task_distribution_by_progress">Task Distribution by Progress</option>
        <option value="task_distribution_by_priority">Task Distribution by Priority</option>
        <!-- <option value="">Task Distribution by Deadline</option> -->
      </select>
      <select
        class="border-none focus:[box-shadow:none] bg-black rounded-md shadow font-medium text-white"
        v-model="timeFilter"
      >
        <option value="last_hour">Last Hour</option>
        <option value="today">Today</option>
        <option value="past_weak">Past Weak</option>
        <option value="past_month">Past Month</option>
        <option value="past_year">Past Year</option>
        <option value="all">All Time</option>
      </select>
    </div>

    <div v-if="statLoading" class="w-full my-24 flex items-center justify-center">
      <v-progress-circular :size="50" :width="5" color="purple" indeterminate></v-progress-circular>
    </div>
    <div
      class="py-10 flex justify-center text-slate-800 text-xl font-medium px-14 text-center bg-slate-100 mt-5 rounded-lg"
      v-else-if="user.completed_tasks == 0 && user.incomplete_tasks == 0"
    >
      <p>Looks like the user haven't added any tasks yet !</p>
    </div>
    <apexchart
      v-else
      class="-z-10 relative mt-5 shadow-md rounded-xl border-black border-2 bg-white"
      :width="currentDivWidth"
      type="donut"
      :options="options"
      :series="series"
    ></apexchart>
  </div>
</template>
<script setup>
import axios from 'axios'
import { computed, onMounted, ref, watchEffect } from 'vue'

const props = defineProps(['user'])
console.log(props.user)

const options = computed(() => ({
  labels: labels.value,
  theme: {
    palette: 'palette7'
  }
}))

const currentDivWidth = computed(() => {
  return document.getElementById('user-stats')?.offsetWidth || 500
})

// onMounted(() => {
//   console.log(currentDivWidth.value)
// })

const statLoading = ref(false)

const series = ref([])
const labels = ref([])

const timeFilter = ref('all')
const statistics = ref('completed_vs_pending_tasks')

watchEffect(async () => {
  try {
    statLoading.value = true
    const res = await axios.get(`/api/user/analysis?admin=true&user_id=${props.user.id}`, {
      params: {
        time_range: timeFilter.value,
        statistics: statistics.value
      }
    })
    // console.log(res.data.series)
    series.value = res.data.series
    labels.value = res.data.labels
    statLoading.value = false
  } catch {
    statLoading.value = false
  } finally {
    statLoading.value = false
  }
})
</script>
<style></style>
