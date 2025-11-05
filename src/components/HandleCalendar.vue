<script setup>
import { computed, ref } from 'vue'
import { ARROW_LEFT, ARROW_RIGHT } from '@/constants'

const props = defineProps({
  language: {
    type: Object,
    required: true,
  },
  date: {
    type: String,
  },
})

const emits = defineEmits(['selectedDate'])

const { MONTH_NAMES, WEEK } = props.language

const currentDate = ref()
currentDate.value = props.date ? new Date(props.date) : new Date()

let selectedDate = new Date(currentDate.value)

const selectedYear = ref(currentDate.value.getFullYear())
const selectedMonth = ref(currentDate.value.getMonth())
const selectedDay = ref(currentDate.value.getDate())

function getDaysInMonth(year, month) {
  return new Date(year, month + 1, 0).getDate()
}

const daysInMonth = computed(() => {
  return getDaysInMonth(currentDate.value.getFullYear(), selectedMonth.value)
})

function nextMonth() {
  currentDate.value.setMonth(currentDate.value.getMonth() + 1)
  selectedMonth.value = currentDate.value.getMonth()
  selectedYear.value = currentDate.value.getFullYear()
}

function previousMonth() {
  currentDate.value.setMonth(currentDate.value.getMonth() - 1)
  selectedMonth.value = currentDate.value.getMonth()
  selectedYear.value = currentDate.value.getFullYear()
}

function selectDate(day) {
  selectedMonth.value = currentDate.value.getMonth()
  selectedDay.value = day
  selectedDate = new Date(selectedYear.value, selectedMonth.value, selectedDay.value)
  emits('selectedDate', selectedDate)
}

const firstDayOfCurrentMonth = computed(() => {
  return new Date(selectedYear.value, selectedMonth.value, WEEK.WEEKDAY_OFFSET).getDay()
})

const lastDateOfPreviousMonth = computed(() => {
  return new Date(selectedYear.value, selectedMonth.value, 0).getDate()
})

const daysBeforeEndWeek = computed(() => {
  return 7 - WEEK.WEEKDAY_OFFSET - new Date(selectedYear.value, selectedMonth.value + 1, 0).getDay()
})

function generatePreviousDays() {
  let result = []
  for (let i = firstDayOfCurrentMonth.value; i > 0; i--) {
    result.push(lastDateOfPreviousMonth.value - i + 1)
  }
  return result
}

function generateNextDays() {
  let result = []
  if (daysBeforeEndWeek.value < 7) {
    for (let i = 1; i <= daysBeforeEndWeek.value; i++) {
      result.push(i)
    }
  }
  return result
}
</script>

<template>
  <div class="calendar">
    <header class="months">
      <span @click="previousMonth" class="arrow">{{ ARROW_LEFT }}</span>
      <span class="monthName">{{ MONTH_NAMES[selectedMonth] }} {{ selectedYear }}</span>
      <span @click="nextMonth" class="arrow">{{ ARROW_RIGHT }}</span>
    </header>
    <main>
      <article class="weeks">
        <span class="week" v-for="value in WEEK.LIST" :key="value">{{ value }}</span>
      </article>
      <div class="days">
        <div class="day non-active-week" v-for="day in generatePreviousDays()" :key="day">
          {{ day }}
        </div>
        <div
          @click="selectDate(day)"
          :class="[
            'day',
            {
              'active-day': selectedDay == day && selectedMonth == selectedDate.getMonth(),
            },
          ]"
          v-for="day in daysInMonth"
          :key="day"
        >
          {{ day }}
        </div>
        <div class="day non-active-week" v-for="day in generateNextDays()" :key="day">
          {{ day }}
        </div>
      </div>
    </main>
  </div>
</template>
<style scoped>
.calendar {
  width: 260px;

  margin: 20vh auto;
  overflow: hidden;

  border-radius: 6%;
  border: 2px solid #000;
}

.months {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 2%;

  height: 40px;

  background-color: #eeeeee;
}

.monthName {
  width: 50%;
  text-align: center;
}

.arrow {
  cursor: pointer;
  user-select: none;

  transition: 50ms;
}

.arrow:hover {
  font-weight: bold;
  scale: 140%;
}

.weeks {
  display: flex;

  margin-inline: 4%;
}

.week {
  display: flex;
  align-items: center;
  justify-content: center;

  width: 14.25%;
  aspect-ratio: 1/1;
}

.non-active-week {
  color: rgba(0, 0, 0, 0.166);
}

.days {
  display: flex;
  flex-wrap: wrap;

  margin: 4%;
}

.day {
  display: flex;
  align-items: center;
  justify-content: center;

  width: 14.25%;
  aspect-ratio: 1/1;

  border-radius: 20%;
}

.active-day {
  background-color: #eeeeee;
}

.day:hover {
  background-color: #eeeeee;
  cursor: pointer;
  user-select: none;
}
</style>
