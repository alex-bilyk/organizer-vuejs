<template>
  <Calendar @set-selected-date="selectedDate = $event" />

  <TodoList :selectedDate="selectedDate" />
</template>

<script>
import Calendar from './components/Calendar.vue'
import TodoList from './components/TodoList.vue'

export default {
  name: 'App',

  components: {
    Calendar,
    TodoList
  },

  data() {
    return {
      selectedDate: ''
    }
  }
}
</script>
