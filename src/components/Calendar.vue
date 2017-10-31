<template>
  <div class="calendar">
{{msg}}
<button v-on:click="test()" class="btn btn-default">test</button>
<!-- {{getWeekDays('sun')}} -->
<!-- <p v-for="day in getDays">{{day}}</p> -->
Day: {{currentDay}}, Month: {{currentMonth}} - {{getMonth[currentMonth]}}, Year: {{currentYear}}

<br>
<button v-on:click="minusMonth()" class="btn btn-default">-</button>
<button v-on:click="plusMonth()" class="btn btn-default">+</button>

    <div>
      <table>
        <tr>
          <th v-for="wday in getDays" style="padding-left: 5px;">{{wday}}</th>
        </tr>
        <tr v-for="week in weeks">
          <td style="padding-left: 5px;" v-for="day in week">{{day.calcNumber}}</td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  name: 'calendar',
  props: ['weekDays', 'nameMonth'],
  data () {
    return {
      msg: 'Calendar',
      errorMsg: '',
      date: new Date(),
      weeks: [],
      currentDay: '',
      currentMonth: '',
      currentYear: '',
    }
  },
  methods:{
    test: function(){
      var self = this
      console.log(self.getDays)
      console.log(self.getMonth)
      
    },
    getDayMonthYear: function()
    {
      var self = this
      self.currentDay = self.date.getDate()
      self.currentMonth =self.date.getMonth()
      self.currentYear = self.date.getFullYear()
    },
    getArrayCalendar: function(){
      var self = this
      var date = new Date(self.currentYear, self.currentMonth)
      self.weeks = []
      self.weeks[0] = []
      for (var i=0; i < self.getNumDay(date); i++)
      {
        self.weeks[0].push({})
      }
      var count = 0
      while (date.getMonth() == self.currentMonth)
      {
        self.weeks[count].push({calcNumber: date.getDate()})
        if (self.getNumDay(date) % 7 == 6)
        {
          count++
          self.weeks[count] = []
        }
        date.setDate(date.getDate()+1)
      }
      console.log(self.weeks)
    },
    getNumDay: function(date){
      var self = this
      var numDay = date.getDay()
      if (self.weekDays == 'mon')
      {
        if (numDay == 0)
        {
          numDay = 6
          return numDay
        }
      }
      else if (self.weekDays == 'ru')
      {
        if (numDay == 0)
        {
          numDay = 6
          return numDay
        }
      }
      else
      {
        return numDay
      }
    },
    plusMonth: function(){
      var self = this
      self.currentMonth += 1
      self.getArrayCalendar()
    },
    minusMonth: function(){
      var self = this
      self.currentMonth -= 1
      self.getArrayCalendar()
    }
  },
  computed: {
    getDays(){
      var self = this
      return getWeekDays(self.weekDays)
    },
    getMonth(){
      var self = this
      return getNameMonth(self.nameMonth)
    }
  },
  created(){
    this.getDayMonthYear()
    this.getArrayCalendar()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
