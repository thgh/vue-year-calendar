<template>
  <div class="cal-year">
    <div class="cal-year__number">
      {{ year }}
    </div>
    <div class="cal-year__months">
      <div class="cal-month" v-for="month in allInOne">
        <div class="cal-month__name">
          {{ monthNames[month.number] }}
        </div>
        <div class="cal-month__daynames">
          <div class="cal-month__dayname" v-for="dayname in weekdayNames">
            {{ dayname }}
          </div>
        </div>
        <div class="cal-month__weeks">
          <div class="cal-week" v-for="week in month.weeks">
            <div class="cal-week__number">{{ week.number }}</div>
            <div class="cal-week__days">
              <div
                class="cal-day"
                :class="dayClasses(day, month.number)"
                v-for="day in week.days"
                :title="day"
              >
                {{ day.getDate() }}
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {
  dayOfYear,
  daysInweek,
  getDay,
  monthsInYear,
  weeksInMonth
 } from './lib.js'

export default {
  name: 'cal-year',
  props: ['year', 'events'],
  computed: {
    activeDOY () {
      return dayOfYear(new Date())
    },
    allInOne () {
      return new Array(12)
        .fill(undefined)
        .map((_, m) => new Date(this.year, m, 1, 2))
        .map(month => ({
          month,
          number: month.getMonth(),
          weeks: weeksInMonth(month)
            .map(week => ({
              week,
              number: this.weekNumber(week),
              days: daysInweek(week)
            }))
        }))
    }
  },
  methods: {
    weekNumber (w) {
      let thursday = new Date(w.getFullYear(), w.getMonth(), w.getDate() - getDay(w) + 3)
      let firstOfYear = new Date(thursday.getFullYear(), 0, 1)
      return Math.ceil( (((thursday - firstOfYear) / 86400000) + getDay(firstOfYear) + 1) / 7 )
    },
    dayClasses (day, calendarMonth) {
      const str = dayOfYear(day)
      const dayMonth = day.getMonth()
      return {
        'cal-day--past': str < this.activeDOY,
        'cal-day--today': str === this.activeDOY,
        'cal-day--in': dayMonth === calendarMonth,
        'cal-day--prev': dayMonth < calendarMonth,
        'cal-day--next': dayMonth > calendarMonth
      }
    },
  },
  created () {
    const month = new Intl.DateTimeFormat(navigator.language || 'nl', { month: 'long' })
    this.monthNames = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11].map(d => month.format(new Date(2002, d, 1)))

    const weekday = new Intl.DateTimeFormat(navigator.language || 'nl', { weekday: 'narrow' })
    this.weekdayNames = [0, 1, 2, 3, 4, 5, 6].map(d => weekday.format(new Date(2002, 0, d)))
  },
  mounted () {
    
  },
  beforeDestroy () {
    
  }
}
</script>
