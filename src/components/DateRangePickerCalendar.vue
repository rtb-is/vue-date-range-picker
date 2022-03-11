<template>
  <div class="daterangepicker-calendar">
    <div class="d-flex align-items-center">
      <div class="p-1" :class="calendarIndex == 1 ? '' : 'invisible'">
        <button type="button" class="calendar-left-button" @mousedown.prevent @click="goToPrevMonth">
          <i class="el-icon">
            <svg class="icon" width="200" height="200" viewBox="0 0 1024 1024" xmlns="http://www.w3.org/2000/svg"><path fill="currentColor" d="M609.408 149.376L277.76 489.6a32 32 0 000 44.672l331.648 340.352a29.12 29.12 0 0041.728 0 30.592 30.592 0 000-42.752L339.264 511.936l311.872-319.872a30.592 30.592 0 000-42.688 29.12 29.12 0 00-41.728 0z"></path></svg>
          </i>
        </button>
      </div>
      <div class="p-1 col text-center label-month">
        {{ displayMonth.format('MMMM YYYY') }}
      </div>
      <div class="p-1" :class="calendarIndex == calendarCount ? '' : 'invisible'">
        <button type="button" class="calendar-right-button" @mousedown.prevent @click="goToNextMonth">
          <i class="el-icon">
            <svg class="icon" width="200" height="200" viewBox="0 0 1024 1024" xmlns="http://www.w3.org/2000/svg"><path fill="currentColor" d="M340.864 149.312a30.592 30.592 0 000 42.752L652.736 512 340.864 831.872a30.592 30.592 0 000 42.752 29.12 29.12 0 0041.728 0L714.24 534.336a32 32 0 000-44.672L382.592 149.376a29.12 29.12 0 00-41.728 0z"></path></svg>
          </i>
        </button>
      </div>
    </div>
    <div class="d-flex justify-content-between text-center daterangepicker-calendar-row daterangepicker-calendar-week">
      <div v-for="day in daysOfFirstWeek" :key="day.format('D')" class="col-day">{{ day.format('ddd') }}</div>
    </div>

    <div class="d-flex flex-wrap justify-content-between text-center daterangepicker-calendar-row">
      <div v-for="day in days" :key="day.format('M-D')" class="col-day" :class="dayClass(day)"
        @mouseover="dayMouseOver(day)" @mousedown.prevent @click="dayClick(day)">
        <span>{{ day.format('D') }}</span>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment'
import { library } from '@fortawesome/fontawesome-svg-core'
import { faCaretLeft, faCaretRight } from '@fortawesome/free-solid-svg-icons'

library.add(faCaretLeft, faCaretRight)

export default {
  props: ['calendarIndex', 'calendarCount', 'month', 'startDate', 'endDate', 'compare', 'startDateCompare', 'endDateCompare', 'step'],
  data: () => {
    return {}
  },
  computed: {
    displayMonth: function() {
      return moment.utc(this.month).add(this.calendarIndex - 1, 'month')
    },
    days: function() {
      const startDay = moment.utc(this.displayMonth).startOf('isoWeek')
      const endDay = moment.utc(this.displayMonth).endOf('month').endOf('isoWeek').startOf('day').add(1, 'day')
      const nDays = moment.duration(endDay.diff(startDay)).asDays()

      const days = []
      let day = 0
      while (day <= nDays) {
        days.push(moment.utc(startDay).add(day, 'day'))
        day++
      }
      return days
    },
    daysOfFirstWeek: function() {
      return this.days.slice(0, 7)
    }
  },
  methods: {
    dayClass: function(day) {
      const classes = []

      // Hide days overflowing
      if (!day.isBetween(this.displayMonth, moment.utc(this.displayMonth).endOf('month'), 'days', '[]')) {
        classes.push('invisible')
      }
      // Class for days between startDate & endDate or is startDate (in case of startDate after endDate)
      if (day.isBetween(this.startDate, this.endDate, 'days', '[]') || day.isSame(this.startDate)) {
        classes.push('daterangepicker-range')
      }
      // Class for days range start
      if (day.isSame(this.startDate)) {
        classes.push('daterangepicker-range-start')
      }
      // Class for days range start
      if (day.isSame(this.endDate)) {
        classes.push('daterangepicker-range-end')
      }
      // Class for days between startDateCompare & endDateCompare or is startDateCompare (in case of startDateCompare after endDateCompare)
      if (this.compare && (day.isBetween(this.startDateCompare, this.endDateCompare, 'days', '[]') || day.isSame(this.startDateCompare))) {
        classes.push('daterangepicker-range-compare')
      }
      // Class for days range start
      if (this.compare && day.isSame(this.startDateCompare)) {
        classes.push('daterangepicker-range-compare-start')
      }
      // Class for days range start
      if (this.compare && day.isSame(this.endDateCompare)) {
        classes.push('daterangepicker-range-compare-end')
      }
      // Class for cursor if the step is selecting something
      if (this.step != null) {
        classes.push('daterangepicker-cursor-pointer')
      }

      return classes.join(' ')
    },
    dayMouseOver: function(day) {
      if (this.step != null) {
        this.selectDate(day)
      }
    },
    dayClick: function(day) {
      if (this.step != null) {
        this.nextStep()
      }
    },
    goToPrevMonth: function() {
      this.$emit('goToPrevMonth')
    },
    goToNextMonth: function() {
      this.$emit('goToNextMonth')
    },
    selectDate: function(date) {
      this.$emit('selectDate', date)
    },
    nextStep: function() {
      this.$emit('nextStep')
    }
  },
  watch: {},
  filters: {}
}
</script>

<style>
.daterangepicker-calendar-row {
  font-size: 12px;
  /*max-width: 230px;*/
}

.daterangepicker-calendar-week {
  border-bottom: solid 1px rgb(235,238,245);
  color: rgb(96, 98, 102) !important;
}

.col-day {
  width: 14.28%;
  padding: 0.5rem 0;
  white-space: nowrap;
  cursor: pointer;
  color: rgb(96, 98, 102) !important;
  font-family: Inter, Roboto, Oxygen, "Fira Sans", "Helvetica Neue", sans-serif !important;
}

.daterangepicker-range {
  background-color: rgba(64,158,255,0.5) !important;
  color: rgb(96, 98, 102) !important;
}
.daterangepicker-range-start {
  color: #ffffff !important;
  background-color: #409eff !important;
}
.daterangepicker-range-end {
  color: #ffffff !important;
  background-color: #409eff !important;
}

.daterangepicker-range-compare {
  background-color: rgba(230,162,60,0.5) !important;
  color: rgb(96, 98, 102) !important;
}

.daterangepicker-range-compare-start {
  background-color: #e6a23c !important;
  color: #ffffff !important;
}

.daterangepicker-range-compare-end {
  background-color: #e6a23c !important;
  color: #ffffff !important;
}

.daterangepicker-range.daterangepicker-range-compare {
  background: linear-gradient(0, #f2d09d 50%, #9fceff 20%);
}
.daterangepicker-range.daterangepicker-range-compare.daterangepicker-range-start.daterangepicker-range-compare-start {
  background: linear-gradient(0, #e6a23c 50%, #409eff 20%);
}
.daterangepicker-range.daterangepicker-range-compare.daterangepicker-range-end.daterangepicker-range-compare-end {
  background: linear-gradient(0, #e6a23c 50%, #409eff 20%);
}
.daterangepicker-range.daterangepicker-range-compare.daterangepicker-range-compare-start {
  background: linear-gradient(0, #e6a23c 50%, #9fceff 20%);
}
.daterangepicker-range.daterangepicker-range-compare.daterangepicker-range-start {
  background: linear-gradient(0, #f2d09d 50%, #409eff 20%);
}
.daterangepicker-range.daterangepicker-range-compare.daterangepicker-range-compare-end {
  background: linear-gradient(0, #e6a23c 50%, #9fceff 20%);
}
.daterangepicker-range.daterangepicker-range-compare.daterangepicker-range-end {
  background: linear-gradient(0, #f2d09d 50%, #409eff 20%);
}

.daterangepicker-cursor-pointer {
  cursor: pointer;
}

.label-month {
  font-weight: bold !important;
  font-family: Inter, Roboto, Oxygen, "Fira Sans", "Helvetica Neue", sans-serif !important;
  color: rgb(96, 98, 102) !important;
}

.el-icon {
  height: 1em;
  width: 1em;
}
.el-icon svg {
  height: 1em;
  width: 1em;
}
.calendar-left-button {
  font-size: 12px;
  border: 0;
  background: transparent;
  cursor: pointer;
  outline: none;
}
.calendar-left-button:hover {
  color: #409eff !important;
}
.calendar-right-button {
  font-size: 12px;
  border: 0;
  background: transparent;
  cursor: pointer;
  outline: none;
}
.calendar-right-button:hover {
  color: #409eff !important;
}
</style>
