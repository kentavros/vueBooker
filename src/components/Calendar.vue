<template>
  <div class="calendar">
{{msg}}
<button v-on:click="test()" class="btn btn-default">test</button>
<!-- Day: {{currentDay}}, Month: {{currentMonth}} - {{getMonth[currentMonth]}}, Year: {{currentYear}} -->

<br>


  <div class="row">
      <div class="shadow col-md-10">
        <div class="title">
          <button v-on:click="minusMonth()" class="btn btn-default">&#9668;</button>
          <p>{{getMonth[currentMonth]}} {{currentYear}}</p>
          <button v-on:click="plusMonth()" class="btn btn-default">&#9658;</button>
        </div>
        <table class="table table-bordered">
          <thead>
          <tr class="info">
            <th v-for="wday in getDays">{{wday}}</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="week in weeks">
            <td class="day" v-for="day in week" :class="{date: day.calcNumber == currentDay}">{{day.calcNumber}}</td>  
          </tr>
          </tbody>
        </table>
      </div>
      <div class="col-md-2">
        2222222222
      </div>
  </div>

  </div>
</template>

<script>
export default {
  name: 'calendar',
  props: ['weekDays', 'nameMonth'],
  data () {
    return {
      msg: 'Calendar component',
      errorMsg: '',
      date: new Date(),
      weeks: [],
      currentMonth: '',
      currentYear: '',
      refresh: false,
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
      self.currentMonth =self.date.getMonth()
      self.currentYear = self.date.getFullYear()
    },
    getArrayCalendar: function(){
      var self = this
      self.weeks = []
      var date = new Date(self.currentYear, self.currentMonth)
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
    },
    getNumDay: function(date){
      var self = this
      var numDay = date.getDay()
      if (self.weekDays == 'mon')
      {
        if (numDay == 0)
        {
          numDay = 7
        }
        return numDay - 1
      }
      if (self.weekDays == 'ru')
      {
        if (numDay == 0)
        {
          numDay = 7
        }
        return numDay - 1
      }
      else
      {
        return numDay
      }
    },
    plusMonth: function(){
      var self = this
      self.currentMonth += 1
      if (self.currentMonth > 11)
      {
        self.currentMonth = 0
        self.currentYear += 1
      }
      self.refresh =false
      self.getArrayCalendar()
      
    },
    minusMonth: function(){
      var self = this
      self.currentMonth -= 1
      if (self.currentMonth < 0){
        self.currentMonth = 11
        self.currentYear -= 1
      }
      self.refresh =false
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
    },
    currentDay(){
      var self = this
      if (self.date.getMonth() == self.currentMonth && self.date.getFullYear() == self.currentYear)
      {
        return self.date.getDate()
      }
      else
      {
        return false
      }
    },
  },
  created(){
    this.getDayMonthYear()
    this.getArrayCalendar()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.shadow {
  padding: 0;
  box-shadow: 0 0 10px rgba(0,0,0,0.5); /* Параметры тени */
/* padding: 10px; */
}
.date{
  background-color: #b1b1da;
  color: darkblue;
  font-weight: bold;
}
.title{
  text-align: center;
  color: darkblue;
  font-size: 17px;
  font-weight: bold;
  background-color: #d9edf7;
  padding-top: 10px;
}
.title p{
  width: 150px;
  display: inline-table;
}
.title button{
  background-color: #d9edf7;
  border-color: #d9edf7;
}
.day{
  cursor: pointer;
  width: 118.33px;
  height: 118.33px;
}
td:hover{
  background: #84b8e1;
}
</style>
