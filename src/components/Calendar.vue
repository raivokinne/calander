<script setup lang="ts">
import { ref, computed } from 'vue'

const currentDate = ref(new Date('2024-05-13'))
const hoveredTime = ref<string | null>(null)
const hoveredDate = ref<number | null>(null)

const availableTimes = ref<Record<string, string[]>>({
  '2024-05-13': ['10:00', '10:30'],
  '2024-05-14': ['12:00', '13:00', '14:00', '15:00'],
  '2024-05-16': ['12:00', '14:30'],
  '2024-05-17': ['16:00'],
})

const weekDays = ['Sv', 'Pr', 'Ot', 'Tr', 'Ce', 'Pk', 'Se']

const formattedMonthYear = computed(() => {
  return currentDate.value.toLocaleString('en', {
    month: 'long',
    year: 'numeric'
  })
})

const currentWeekDates = computed(() => {
  const dates = []
  const firstDayOfWeek = new Date(currentDate.value)
  const day = currentDate.value.getDay()
  firstDayOfWeek.setDate(currentDate.value.getDate() - day + (day === 0 ? -6 : 1))

  for (let i = 0; i < 7; i++) {
    dates.push(new Date(firstDayOfWeek))
    firstDayOfWeek.setDate(firstDayOfWeek.getDate() + 1)
  }
  return dates
})

const getTimesForDate = (date: Date) => {
  const formattedDate = date.toISOString().split('T')[0]
  return availableTimes.value[formattedDate] || []
}

const prevMonth = () => {
  const newDate = new Date(currentDate.value)
  newDate.setMonth(newDate.getMonth() - 1)
  currentDate.value = newDate
}

const nextMonth = () => {
  const newDate = new Date(currentDate.value)
  newDate.setMonth(newDate.getMonth() + 1)
  currentDate.value = newDate
}

const prevWeek = () => {
  const newDate = new Date(currentDate.value)
  newDate.setDate(newDate.getDate() - 7)
  currentDate.value = newDate
}

const nextWeek = () => {
  const newDate = new Date(currentDate.value)
  newDate.setDate(newDate.getDate() + 7)
  currentDate.value = newDate
}
</script>

<template>
  <div class="w-[800px] rounded-3xl flex flex-col gap-4">
    <div class="bg-[#1F3B2B] text-white rounded-full h-[60px] px-8 py-6 flex justify-between items-center">
      <button @click="prevMonth" class="text-4xl font-light hover:opacity-75 transition-opacity">‹</button>
      <h2 class="text-2xl font-bold capitalize">{{ formattedMonthYear }}</h2>
      <button @click="nextMonth" class="text-4xl font-light hover:opacity-75 transition-opacity">›</button>
    </div>

    <div class="relative bg-white overflow-hidden rounded-3xl border border-gray-400">
      <div class="absolute top-4 left-0 right-0 px-4 flex justify-between items-center z-10">
        <button @click="prevWeek" class="text-2xl text-gray-400 hover:text-gray-600">‹</button>
        <button @click="nextWeek" class="text-2xl text-gray-400 hover:text-gray-600">›</button>
      </div>

      <div class="grid grid-cols-7">
        <div v-for="(date, index) in currentWeekDates"
             :key="index"
             @mouseover="hoveredDate = index"
             @mouseleave="hoveredDate = null"
             class="flex flex-col hover:bg-gray-100">
          <div :class="[
            'py-2 px-2 flex flex-col items-center transition-colors duration-200 rounded-3xl',
          ]">
            <span class="text-sm text-gray-500">{{ weekDays[date.getDay()] }}</span>
            <span class="text-xl font-semibold">{{ date.getDate() }}</span>
          </div>
          <div class="px-2 py-8 h-full flex flex-col border-l border-t border-gray-300 gap-3">
            <button v-for="time in getTimesForDate(date)"
                    :key="time"
                    @mouseover="hoveredTime = `${date.toISOString().split('T')[0]}-${time}`"
                    @mouseleave="hoveredTime = null"
                    :class="[
                      'py-2.5 px-6 rounded-full border border-black text-sm transition-all duration-200 font-medium',
                      hoveredTime === `${date.toISOString().split('T')[0]}-${time}`
                        ? 'bg-[#1F3B2B] text-white border-transparent'
                        : 'bg-white border-gray-200 hover:border-[#1F3B2B]'
                    ]">
              {{ time }}
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
