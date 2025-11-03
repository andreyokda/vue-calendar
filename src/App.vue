<template>
  <div id="app">
    <div class="container">
      <h1>Календарь</h1>
      
      <div class="demo-controls">
        <label>Выбор языка:
          <select v-model="currentLanguage">
            <option value="en">English</option>
            <option value="ru">Русский</option>
          </select>
        </label>
        <div><button @click="resetCalendar" class="reset-btn">Сбросить</button></div>
      </div>

      <div v-if="selectedDate" class="selected-date">
        <strong>Выбранная дата:</strong> {{ selectedDate }}
      </div>

      <div class="calendar-container">
        <Calendar 
          :initial-date="initialDate"
          :language="currentLanguage"
          @date-selected="handleDateSelect"
        />
      </div>

    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Calendar from './components/Calendar.vue'

const currentLanguage = ref('ru')
const initialDate = ref('')
const selectedDate = ref('')

const handleDateSelect = (date) => {
  selectedDate.value = date
  console.log('Date selected:', date)
}

const resetCalendar = () => {
  initialDate.value = ''
  selectedDate.value = ''
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  font-family: Arial, sans-serif;
  padding: 20px;
  background-color: #f8f9fa;
  min-height: 100vh;
}

.container {
  max-width: 800px;
  margin: 0 auto;
}

h1 {
  text-align: center;
  margin-bottom: 30px;
  color: #333;
}

.demo-controls {
  display: flex;
  flex-direction: column;
  gap: 15px;
  margin-bottom: 30px;
  padding: 20px;
  background: white;
  border: 1px solid #0056b3;
}

.demo-controls label {
  display: flex;
  flex-direction: column;
  gap: 5px;
  font-weight: bold;
}

.demo-controls select,
.demo-controls input {
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
}

.reset-btn {
  padding: 8px 16px;
  background: #dc3545;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  align-self: flex-start;
}

.reset-btn:hover {
  background: #c82333;
}

.calendar-container {
  margin-bottom: 20px;
}

.selected-date {
  text-align: center;
  padding: 16px;
  background: white;
  border: 1px solid #0056b3;
  font-size: 1.1rem;
  margin-bottom: 20px;
}

@media (max-width: 600px) {
  .demo-controls {
    align-items: stretch;
  }
}
</style>