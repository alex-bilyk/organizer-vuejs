<template>
  <div>
    <div class="panel">
      <button @click="changeMonth(false)">Prev</button>
      <h1>{{ `${selectedMonthName} ${selectedYear}` }}</h1>
      <button @click="changeMonth(true)">Next</button>
    </div>

    <div class="table">
      <div class="table__heading">
        <div
          v-for="(day, index) in weekDays"
          :key="index"
          class="table__heading__item"
        >
          {{ day }}
        </div>
      </div>

      <div class="table__body">
        <div
          v-for="(day, index) in getDays(selectedYear, viewMonth.getMonth())"
          :key="index"
          class="table__body__item"
          :class="{
            'active': momentFunc(new Date()).format('YYYY-MM-DD') === momentFunc(new Date(day.date)).format('YYYY-MM-DD'),
            'selected': momentFunc(selectedDate).format('YYYY-MM-DD') === momentFunc(new Date(day.date)).format('YYYY-MM-DD')
          }"
          @click="setSelectedDate(day.date)"
        >
          {{ day.title }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment'

export default {
  name: 'Calendar',

  data() {
    return {
      momentFunc: moment,
      weekDays: moment.weekdaysShort(),
      selectedDate: new Date(),
      viewMonth: new Date()
    }
  },

  computed: {
    selectedYear() {
      return moment(this.viewMonth).format('YYYY')
    },

    selectedMonthName() {
      return moment(this.viewMonth).format('MMMM')
    }
  },

  created() {
    this.$emit('set-selected-date', this.selectedDate)
  },

  methods: {
    getDays(year, month) {
      const date = new Date(year, month, 1)
      const result = []

      while (date.getMonth() === month) {
        result.push({
          date: date.toJSON(),
          title: `${this.weekDays[date.getDay()]} ${date.getDate()}`,
        })
        date.setDate(date.getDate() + 1)
      }

      if (result.length !== 28) {
        let prevMonthResult = []
        const prevMonthDate = (month === 0 ? new Date(year - 1, 11, 1) : new Date(year, month - 1, 1))

        month = month === 0 ? 11 : month - 1

        while (prevMonthDate.getMonth() === month) {
          prevMonthResult.push({
            date: prevMonthDate.toJSON(),
            title: `${this.weekDays[prevMonthDate.getDay()]} ${prevMonthDate.getDate()}`
          })

          prevMonthDate.setDate(prevMonthDate.getDate() + 1)
        }

        prevMonthResult = prevMonthResult.slice(-(35 - result.length)).reverse()

        prevMonthResult.forEach(item => result.unshift(item))
      }

      return result
    },

    changeMonth(isNext = false) {
      this.viewMonth = new Date(moment(this.viewMonth).add(isNext ? 1 : -1, 'months'))

      if (moment(`${this.selectedYear}-${this.selectedMonthName}`, 'YYYY-MMMM').daysInMonth() >= moment(this.selectedDate).format('D')) {
        this.setSelectedDate(moment(`${this.selectedYear}-${this.selectedMonthName}-${moment(this.selectedDate).format('D')}`).toJSON())
      } else {
        this.setSelectedDate(moment(`${this.selectedYear}-${this.selectedMonthName}-1`).toJSON())
      }
    },

    setSelectedDate(val) {
      this.selectedDate = new Date(val)
      this.$emit('set-selected-date', val)
    }
  }
}
</script>