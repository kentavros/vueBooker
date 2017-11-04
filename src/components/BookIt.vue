<template>
  <div class="bookIt">
    <router-link class="toMain" to="/"><button class="btn btn-info">To main</button></router-link>
    <div class="title1">
      Boardroom Booker
    </div>
    <div class="form">
      <p class="alert-danger" style="text-align: center;">{{errorMsg}}</p>
      <fieldset class="fields">
          <div class="nameRoom">
              {{room.name}}
          </div>
            <div class="control-group">
              <!-- User selected -->
              <div class="controls">
                  <p class="help-block">1. Booked for:</p>
                  <select v-model="bookedUser">
                      <option v-for="user in usersList" :value="user.id">{{user.username}}</option>
                  </select>
              </div>
            </div>
            <div class="control-group">
              <!-- Meeting -->
              <div class="controls">
                  <p class="help-block">2. I would like to book the meeting:</p>
                  <select >
                      <option>Jan</option>
                  </select>
                  <select >
                      <option>8</option>
                  </select>
                  <select >
                      <option>2004</option>
                  </select>
              </div>
            </div>
            <div class="control-group">
              <!-- Time Meeting -->
              <div class="controls">
                  <p class="help-block">3. Specify what the time and end of the meeting:</p>
                  <select >
                      <option >8</option>
                  </select>
                  <select >
                      <option >00</option>
                  </select>
                  <select >
                      <option >AM</option>
                  </select>
              </div>
              <br>
              <div class="controls">
                  <select >
                      <option >8</option>
                  </select>
                  <select >
                      <option >00</option>
                  </select>
                  <select >
                      <option >AM</option>
                  </select>
              </div>
            </div>
            <div class="control-group">
              <!-- Description Meeting -->
              <div class="controls">
                  <p class="help-block">4. Enter the specifics for the meeting:</p>
                  <textarea rows="4" cols="25" name="description">
                  </textarea>
              </div>
            </div>
            <div class="control-group">
              <!-- Recurring Meeting -->
              <div class="controls">
                <p class="help-block">5. Is this going to be a recurring event?</p>
                <input type="radio" id="no" value="no" >
                <label for="no">no</label>
                <br>
                <input type="radio" id="yes" value="yes" >
                <label for="yes">yes</label>
              </div>
            </div>
            <div class="control-group">
              <!-- Periodicity Meeting -->
              <div class="controls">
                <p class="help-block">6. If it is recurring, specify weekly, bi-weekly, or monthly.</p>
                <input type="radio" id="weekly" value="weekly" >
                <label for="weekly">weekly</label>
                <br>
                <input type="radio" id="bi-weekly" value="bi-weekly" >
                <label for="bi-weekly">bi-weekly</label>
                <br>
                <input type="radio" id="monthly" value="monthly" >
                <label for="monthly">monthly</label>
                <p class="help-block">If weekly or bi-weekly, specify the number of weeks for it to keep recurring.
                  If monthly, specify the number of months. (If you choose "bi-weekly" and put in an odd number of weeks, the computer will round down.)
                </p>
                <br>
                <input type="text" id="numPeriod" class="input-xlarge" style="width: 30px;">
                <label for="numPeriod">Duration (max )</label>
              </div>
            </div>
            <div class="buttons control-group">
            <!-- Button -->
            <div class="controls">
                <button class="btn btn-success">Submit</button>
                <router-link to="/"><button class="btn btn-info">Cancel</button></router-link>
            </div>
            </div>
      </fieldset>
    </div>
    </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'BookItForm',
  data () {
    return {
      id: '',
      msg: '',
      errorMsg: '',
      room: {},
      user: {},
      users: [],
      bookedUser: '',
      // meetingDate: []
    }
  },
  methods:{
    getRoom: function(id){
      var self = this
      axios.get(getUrl() + 'rooms/hash/' + self.user.hash + '/id_user/' + self.user.id +
      '/id/' + id, true)
            .then(function (response) {
            if (Array.isArray(response.data))
            {
              self.room = response.data[0]
            }
            else{
              self.errorMsg = response.data
            }
        })
        .catch(function (error) {
          console.log(error)
        })
    },
    checkUserFun: function(){
      var self = this
      if (localStorage['user'])
      {
        self.user = JSON.parse(localStorage['user'])
        self.bookedUser = self.user.id
        axios.get(getUrl() + 'users/hash/' + self.user.hash + '/id_user/' + self.user.id +
        '/id/' + self.user.id, true)
            .then(function (response) {
              if (Array.isArray(response.data))
              {
                if (self.user.hash === response.data[0].hash)
                {
                  self.user = response.data[0]

                  self.getUsers()
                }
              }
              else 
              {
                self.$router.push('/')
              }
            })
            .catch(function (error) {
              console.log(error)
            });
      }
      else{
        self.$router.push('/')
      }
    },
    getUsers:function(){
      var self = this
      if (self.user.role != 'admin')
      {
        return false
      }
      // self.msg = ''
      // self.errorMsg = ''
      axios.get(getUrl() + 'users/hash/' + self.user.hash + '/id_user/' + self.user.id)
          .then(function (response) {
          // console.log(response.data)
          if (Array.isArray(response.data))
          {
            self.users = response.data
            console.log(self.users)
          }
          else{
            self.errorMsg = response.data
          }
      })
      .catch(function (error) {
        console.log(error)
      })

    },
  },
  created(){
    this.checkUserFun()
    this.id = this.$route.params.id
    this.getRoom(this.id)
    // this.getUsers()
  },
  computed: {
    usersList(){
      var self = this 
      if (self.users.length != 0)
      {
        return self.users
      }
      else
      {
        var arr = []
        arr.push(self.user)
        return arr
      }
    }
  }
}
</script>

<style scoped>
.title1{
  font-size: 35px;
  text-align: center;
}
.nameRoom{
  width: 200px;
  text-align: center;
  font-size: 24px;
  border: 1px solid;
  margin: auto;
}
.form{
  /* position: relative;
  top: 30px; */
  margin: auto;
  color: darkblue;
  width: 900px;
  background-color: rgba(255, 255, 255, 0.7);
  padding-bottom: 10px;
}
.help-block{
  color: black;
  margin-top: 20px;
  margin-bottom: 20px;
}
.buttons{
  margin-top: 30px;
  margin-bottom: 40px;
}
.fields{
  padding-top: 20px;
  padding-left: 30px;
}
</style>
