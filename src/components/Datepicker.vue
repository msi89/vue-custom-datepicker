<template>
  <div class="datepicker" @click.stop>
    <input type="text" :value="formatedValue" @focus="show()" />
    <div :class="['calendar', { show: visible }]">
      <div class="calendar__header">
        <div class="calendar__year">{{ year }}</div>
        <div class="calendar__date">{{ formatedDate }}</div>
      </div>
      <div class="calendar__body">
        <!-- month -->
        <div class="calendar__month">
          <button class="calendar__month__prev" @click="prev()">&#8227;</button>
          <span class="calendar__month__value">{{ formatedMonth }}</span>
          <button class="calendar__month__next" @click="next()">&#8227;</button>
        </div>
        <!-- week days -->
        <div class="calendar__weeks">
          <div
            class="calendar__weekday"
            v-for="(weekday, w) in weekdays"
            :key="w"
          >
            {{ weekday }}
          </div>
        </div>
        <!-- days of month -->
        <div class="calendar__days">
          <div
            class="calendar__day_spacer"
            :style="{ gridColumn: `span ${startweek}` }"
          ></div>
          <div
            :class="['calendar__day', { selected: active(day) }]"
            v-for="(day, d) in days"
            :key="d"
            @click="select(day)"
          >
            {{ day.date() }}
          </div>
        </div>
      </div>
      <div class="calendar__footer">
        <button @click="hide()">Cancel</button>
        <button @click="submit()">Ok</button>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment'

export default {
  props: {
    value: { type: String, required: true },
    format: { type: String, default: 'YYYY-MM-DD' },
    color: { type: String, default: 'coral' }
  },
  data() {
    return {
      date: moment(this.value || moment(), 'YYYY-MM-DD'),
      formatedValue: this.value,
      visible: false
    }
  },
  computed: {
    year() {
      return this.date.year()
    },
    weekdays() {
      return moment.weekdaysMin()
    },
    days() {
      const endDay = this.date
        .clone()
        .endOf('month')
        .date()
      return Array(endDay)
        .fill()
        .map((_, idx) => moment([this.year, this.date.month(), idx + 1]))
    },
    startweek() {
      return this.date
        .clone()
        .startOf('month')
        .day()
    },
    formatedMonth() {
      return this.date.format('MMMM YYYY')
    },
    formatedDate() {
      return this.date.format('dddd, DD MMMM')
    }
  },
  methods: {
    active(date) {
      return this.date.unix() === date.unix()
    },
    next() {
      let _month = this.date.month() + 1
      let _year = this.year
      if (_month > 11) {
        _year++
        _month = 0
      }
      this.date = moment([_year, _month])
    },
    prev() {
      let _month = this.date.month() - 1
      let _year = this.year
      if (_month < 0) {
        _year--
        _month = 0
      }
      this.date = moment([_year, _month])
    },
    select(date) {
      this.date = date
    },
    show() {
      this.visible = true
      setTimeout(() => document.addEventListener('click', this.hide), 200)
    },
    hide() {
      this.visible = false
      document.removeEventListener('click', this.hide)
    },
    submit() {
      this.formatedValue = this.date.format(this.format)
      this.$emit('change', this.formatedValue)
      this.hide()
    }
  }
}
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Open Sans', sans-serif;
}
.datepicker {
  position: relative;
}
.datepicker button {
  outline: none;
  border: 0;
  background: transparent;
  cursor: pointer;
  transition: all 0.2s ease-in-out;
}
.calendar {
  z-index: 9;
  position: absolute;
  width: 260px;
  top: 100%;
  box-shadow: 0px 14px 45px rgba(0, 0, 0, 0.25),
    0px 10px 18px rgba(0, 0, 0, 0.22);
  background: #fff;
  visibility: hidden;
  opacity: 0;
  transform: translateY(-20px);
  transition: all 0.3s linear;
}
.calendar.show {
  visibility: visible;
  opacity: 1;
  transform: translateY(0px);
}

.calendar__header {
  padding: 15px 10px;
  background: rebeccapurple;
  color: #fff;
}
.calendar__year {
  opacity: 0.6;
  font-size: 1rem;
  line-height: 1.2rem;
}
.calendar__date {
  font-size: 1.2rem;
  line-height: 1.5rem;
}

.calendar__month {
  padding: 5px 10px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.calendar__month button {
  width: 22px;
  height: 22px;
  border-radius: 50%;
  font-size: 1.2rem;
  line-height: 22px;
  color: #555;
  text-align: center;
  background: rgba(0, 0, 0, 0.05);
}
.calendar__month button:hover {
  background: rgba(0, 0, 0, 0.2);
}
.calendar__month__prev {
  transform: rotate(180deg);
}

.calendar__weeks,
.calendar__days {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
}
.calendar__days {
  gap: 5px;
}
.calendar__day,
.calendar__weekday {
  text-align: center;
  font-size: 14px;
}
.calendar__weekday {
  opacity: 0.8;
  font-weight: 300;
}
.calendar__day {
  width: 32px;
  height: 32px;
  line-height: 32px;
  font-weight: 300;
  cursor: pointer;
  border-radius: 50%;
  transition: all 0.3s ease-in-out;
}
.calendar__day.selected {
  background: rebeccapurple;
  color: #fff;
}

.calendar__day:hover {
  background: rebeccapurple;
  color: #fff;
  opacity: 0.8;
}

.calendar__footer {
  padding: 10px;
  text-align: right;
}
.calendar__footer button {
  padding: 8px 10px;
  text-transform: uppercase;
  color: rebeccapurple;
  opacity: 0.9;
}
.calendar__footer button:hover {
  opacity: 1;
}
</style>
