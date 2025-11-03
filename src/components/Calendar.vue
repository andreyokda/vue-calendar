<template>
  <div class="calendar">
    <div class="calendar-header">
      <button @click="prevMonth" class="nav-button">&lt;</button>
      <h2>{{ currentMonthYear }}</h2>
      <button @click="nextMonth" class="nav-button">&gt;</button>
    </div>

    <div class="weekdays">
      <div 
        v-for="day in weekdays" 
        :key="day" 
        class="weekday"
      >
        {{ day }}
      </div>
    </div>

    <div class="days">
      <div 
        v-for="day in calendarDays" 
        :key="day.date.toString()"
        :class="[
          'day',
          {
            'current-month': day.isCurrentMonth,
            'selected': isSelected(day.date),
            'today': isToday(day.date)
          }
        ]"
        @click="selectDate(day)"
      >
        {{ day.day }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue'

const props = defineProps({
  initialDate: {
    type: String,
    default: null
  },
  language: {
    type: String,
    default: 'en'
  }
})

const emit = defineEmits(['date-selected'])

const currentDate = ref(new Date())
const selectedDate = ref(null)

const locales = {
  en: {
    months: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
    weekdays: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']
  },
  ru: {
    months: ['Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь', 'Июль', 'Август', 'Сентябрь', 'Октябрь', 'Ноябрь', 'Декабрь'],
    weekdays: ['Вс', 'Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб']
  }
}

const currentMonthYear = computed(() => {
  const month = locales[props.language].months[currentDate.value.getMonth()]
  const year = currentDate.value.getFullYear()
  return `${month} ${year}`
})

const weekdays = computed(() => {
  return locales[props.language].weekdays
})

const calendarDays = computed(() => {
  const year = currentDate.value.getFullYear()
  const month = currentDate.value.getMonth()
  
  const firstDay = new Date(year, month, 1)
  const lastDay = new Date(year, month + 1, 0)
  
  const firstDayOfWeek = firstDay.getDay()
  
  const prevMonthLastDay = new Date(year, month, 0).getDate()
  
  const days = []
  
  for (let i = firstDayOfWeek - 1; i >= 0; i--) {
    const day = prevMonthLastDay - i
    days.push({
      day: day,
      date: new Date(year, month - 1, day),
      isCurrentMonth: false
    })
  }
  
  for (let i = 1; i <= lastDay.getDate(); i++) {
    days.push({
      day: i,
      date: new Date(year, month, i),
      isCurrentMonth: true
    })
  }
  
  const totalCells = 42
  const nextMonthDays = totalCells - days.length
  for (let i = 1; i <= nextMonthDays; i++) {
    days.push({
      day: i,
      date: new Date(year, month + 1, i),
      isCurrentMonth: false
    })
  }
  
  return days
})

const prevMonth = () => {
  currentDate.value = new Date(
    currentDate.value.getFullYear(),
    currentDate.value.getMonth() - 1,
    1
  )
}

const nextMonth = () => {
  currentDate.value = new Date(
    currentDate.value.getFullYear(),
    currentDate.value.getMonth() + 1,
    1
  )
}

const selectDate = (day) => {
  if (day.isCurrentMonth) {
    selectedDate.value = day.date
    emit('date-selected', formatDate(day.date))
  }
}

const isSelected = (date) => {
  if (!selectedDate.value) return false
  return formatDate(date) === formatDate(selectedDate.value)
}

const isToday = (date) => {
  return formatDate(date) === formatDate(new Date())
}

const formatDate = (date) => {
  const year = date.getFullYear()
  const month = String(date.getMonth() + 1).padStart(2, '0')
  const day = String(date.getDate()).padStart(2, '0')
  return `${year}-${month}-${day}`
}

const parseDateString = (dateString) => {
  if (!dateString) return null
  
  try {
    const [year, month, day] = dateString.split('-').map(Number)
    const date = new Date(year, month - 1, day)
    if (date.getFullYear() === year && date.getMonth() === month - 1 && date.getDate() === day) {
      return date
    }
    return null
  } catch (error) {
    console.error('Invalid date format:', dateString)
    return null
  }
}

const initializeCalendar = () => {
  const parsedDate = parseDateString(props.initialDate)
  
  if (parsedDate) {
    currentDate.value = new Date(parsedDate.getFullYear(), parsedDate.getMonth(), 1)
    selectedDate.value = parsedDate
    emit('date-selected', formatDate(parsedDate))
  } else {
    const today = new Date()
    currentDate.value = new Date(today.getFullYear(), today.getMonth(), 1)
    selectedDate.value = today
    emit('date-selected', formatDate(today))
  }
}

watch(() => props.initialDate, (newDate) => {
  const parsedDate = parseDateString(newDate)
  if (parsedDate) {
    currentDate.value = new Date(parsedDate.getFullYear(), parsedDate.getMonth(), 1)
    selectedDate.value = parsedDate
  }
})

onMounted(() => {
  initializeCalendar()
})
</script>

<style scoped>
.calendar {
  width: 100%;
  max-width: 400px;
  margin: 0 auto;
  font-family: Arial, sans-serif;
  overflow: hidden;
  border: 1px solid #0056b3;
}

.calendar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px;
  background-color: #f5f5f5;
  border-bottom: 1px solid #0056b3;
}

.calendar-header h2 {
  margin: 0;
  font-size: 1.2rem;
  color: #333;
}

.nav-button {
  background: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 8px 12px;
  cursor: pointer;
  font-size: 1rem;
  transition: background-color 0.2s;
}

.nav-button:hover {
  background: #0056b3;
}

.weekdays {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  background-color: #f8f9fa;
  border-bottom: 1px solid #e0e0e0;
}

.weekday {
  padding: 12px 8px;
  text-align: center;
  font-weight: bold;
  font-size: 0.9rem;
  color: #666;
}

.days {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 1px;
  background-color: #e0e0e0;
}

.day {
  padding: 12px 8px;
  text-align: center;
  background-color: white;
  cursor: pointer;
  transition: all 0.2s;
  min-height: 60px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.day:hover {
  background-color: #f0f0f0;
}

.day.current-month {
  color: #333;
}

.day:not(.current-month) {
  color: #ccc;
  cursor: not-allowed;
}

.day.selected {
  background-color: #007bff;
  color: white;
}

.day.today {
  background-color: #e3f2fd;
  font-weight: bold;
}

.day.selected.today {
  background-color: #007bff;
  color: white;
}
</style>