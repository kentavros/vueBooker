<template>
  <div class="main">
    <div v-if="checkUser == ''">
      <login></login>
    </div>
    <div v-else>
      Hello <strong>{{user.login}}</strong> - Main!
      <calendar :weekDays="'sun'" :nameMonth="'en'"></calendar>
    </div>
    

  

  </div>
</template>

<script>
import axios from 'axios'
import Login from './Login'
import Calendar from './Calendar'
export default {
  name: 'Main',
  data () {
    return {
      checkUser: '',
      role: '',
      user: {},
    }
  },
  methods: {
    checkUserFun: function()
    {
      var self = this
      if (localStorage['user'])
      {
        self.user = JSON.parse(localStorage['user'])
        axios.get(getUrl() + 'users/' + self.user.id)
            .then(function (response) {
              console.log(response.data)
              if (Array.isArray(response.data)){
                if (self.user.hash === response.data[0].hash)
                {
                    self.checkUser = 1;
                    self.user.login = response.data[0].login
                    self.user.role = response.data[0].role
                    return true
                }
                else
                {
                  delete localStorage['user']
                  self.checkUser = ''
                  return false
                }
              }
              else {
                delete localStorage['user']
                self.errorMsg = response.data
                return false
              }
            })
            .catch(function (error) {
              console.log(error)
            });
      }
      else
      {
        self.checkUser = ''
        return false
      }
    }
  },
  created(){
    this.checkUserFun()
  },
  components: {
    'Login': Login,
    'Calendar': Calendar
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
