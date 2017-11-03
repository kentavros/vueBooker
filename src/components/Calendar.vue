<template>
  <div class="calendar">
    <p class="alert-danger" style="text-align: center;">{{errorMsg}}</p>
    <div class="row">
      <div class="shadow col-md-10">
          <p class="rooms">
            <button v-for="(room,index) in rooms" v-on:click="selRoomFun(index)" class="btnRoom btn btn-info btn-lg" 
            :class="{selBtn: room.id == selRoom.id}">
              {{room.name}}
            </button>
          </p>
          <p class="roomSel">
            Your location is: <strong>{{selRoom.name}}</strong>
          </p>
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
        <div class="menu">
          <div class="right-top-menu">
            <div class="ru-en-btn">
              <button v-if="nameMonth == 'en'" v-on:click="getRu()" class="btn btn-info">Ru</button>
              <button v-else-if="nameMonth == 'ru'" v-on:click="getEn()" class="btn btn-info">En</button>
            </div>
            <div class="mon-sun-btn">
              <button v-if="weekDays == 'sun'" v-on:click="firstMonday()" class="btn btn-info">Monday First</button>
              <button v-else-if="weekDays == 'mon'" v-on:click="firstSunday()" class="btn btn-info">Sunday First</button>
            </div>
          </div>
          <div class="btn-Book-Emp">
            <button v-on:click="test('Book it!')" class="btn btn-success">Book It!</button>
            <router-link v-if="role == 'admin'" to="/emplist">
              <button class="btn btn-danger">Employee List</button>
            </router-link>
          </div>

        </div>
      </div>
  </div>

  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'calendar',
  props: ['role'],
  data () {
    return {
      msg: '',
      errorMsg: '',
      date: new Date(),
      weeks: [],
      currentMonth: '',
      currentYear: '',
      weekDays: 'sun',
      nameMonth: 'en',
      rooms: [],
      selRoom: {}
    }
  },
  methods:{
    selRoomFun: function(index){
      var self = this
      self.selRoom = self.rooms[index]
    },
    getRooms: function(){
      var self = this
      axios.get(getUrl() + 'rooms/')
          .then(function (response) {
          // console.log(response.data)
          if (Array.isArray(response.data))
          {
            self.rooms = response.data
            self.selRoom = self.rooms[0]
          }
          else{
            self.errorMsg = response.data
          }
      })
      .catch(function (error) {
        console.log(error)
      })
    },
    getMonthYear: function()
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
      self.getArrayCalendar()
      
    },
    minusMonth: function(){
      var self = this
      self.currentMonth -= 1
      if (self.currentMonth < 0){
        self.currentMonth = 11
        self.currentYear -= 1
      }
      self.getArrayCalendar()
    },
    firstMonday: function(){
      var self = this 
      self.weekDays = 'mon'
      self.getArrayCalendar()
    },
    firstSunday: function(){
      var self = this
      self.weekDays = 'sun'
      self.getArrayCalendar()
    },
    getRu: function(){
      var self = this
      self.nameMonth = 'ru'
      self.weekDays = 'ru'
      self.getArrayCalendar()
    },
    getEn: function(){
      var self = this
      self.nameMonth = 'en'
      self.weekDays = 'sun'
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
    this.getMonthYear()
    this.getArrayCalendar()
    this.getRooms()
  }
}
</script>

<style scoped>
.shadow {
  padding: 0;
  box-shadow: 0 0 10px rgba(0,0,0,0.5); /* Параметры тени */
}
.date{
  background-color: #b1b1da;
  color: #b12d1f;
  font-weight: bold;
}
tbody{
  background-color: white;
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
  background: #c7e3f1;
}
.ru-en-btn{
  margin-bottom: 15px;
}
.mon-sun-btn{
  margin-bottom: 15px;
  width: 115px;
}
.right-top-menu{
  height: 100px;
}
.btn-Book-Emp{
  height: 300px;
}
.btn-Book-Emp button{
  margin-top: 70px;
  width: 115px;
}
.btnRoom{
  border-radius: 0;
}
.selBtn{
    color: red;
}
.rooms{
  margin: 0;
  background-color: #d9edf7;
  text-align: center;
}
.roomSel{
  margin: 0;
  background-color: #d9edf7;
  text-align: center;
  color: darkblue;
  font-size: 18px;
}
</style>
