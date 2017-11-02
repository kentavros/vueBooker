<template>
  <div class="empList">
    <div v-if="addUser == ''" class="usersList">
      <div id="legend">
            <legend class="title">Employee List</legend>
      </div>
      <p class="alert-danger">{{errorMsg}}</p>
      <p class="alert-success">{{msg}}</p>
       <table class="table table-hover">
         <thead>
           <tr class="info">
             <th class="thTable">ID</th>
             <th class="thTable">login</th>
             <th class="thTable">email</th>
             <th class="thTable">role</th>
             <th class="thTable">Edit</th>
             <th class="thTable">Remove</th>
           </tr>
         </thead>
         <tbody>
           <tr v-for="(u, index) in users">
             <td>{{u.id}}</td>
             <td><a :href="'mailto:'+u.email">{{u.login}}</a></td>
             <td><a :href="'mailto:'+u.email">{{u.email}}</a></td>
             <td>{{u.role}}</td>
             <td><button class="btn alert-info" v-on:click="test(index)">Edit</button></td>
             <td><button class="btn alert-danger" v-on:click="removeUser(index)">Remove</button></td>
           </tr>
         </tbody>
       </table>
       <router-link to="/emplist/addnew/"><button v-on:click="addUser=1" class="btn btn-success" >Add a new Employee</button></router-link>
       <router-link to="/"><button class="btn btn-info">Back</button></router-link>
    </div>
    <div v-else>
      <router-view></router-view>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'EmployeeList',
  data () {
    return {
      msg: '',
      errorMsg: '',
      addUser: '',
      user: {},
      users: [],
    }
  },
  methods: {
    test:function(str){
      var self = this
      alert(str)
      console.log(self.users[str].login)
    },
    removeUser: function(index){
      var self = this
      self.msg = ''
      self.errorMsg = ''
      var result = confirm('Do you want to remove user "'+ self.users[index].login + '"?');
      if (!result)
      {
        return false
      }
      axios.delete(getUrl() + 'users/hash/' + self.user.hash + '/id_user/' + self.user.id +
        '/id/' + self.users[index].id)
          .then(function (response) {
          // console.log(response.data)
          if (response.data === 1)
          {
            self.msg = 'User with login "' + self.users[index].login + '" Delete - Success!'
            self.getUsersList()
          }
          else{
            self.errorMsg = response.data
            return false;
          }
      })
      .catch(function (error) {
        console.log(error);
      })
    },
    getUsersList:function(){
      var self = this
      self.addUser = ''
      axios.get(getUrl() + 'users/hash/' + self.user.hash + '/id_user/' + self.user.id)
          .then(function (response) {
          // console.log(response.data)
          if (Array.isArray(response.data))
          {
            self.users = response.data
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
        axios.get(getUrl() + 'users/' + self.user.id)
            .then(function (response) {
              // console.log(response.data)
              if (Array.isArray(response.data))
              {
                if (self.user.hash === response.data[0].hash)
                {
                  if (response.data[0].role == 'admin')
                  {
                    self.user.role = response.data[0].role
                  }
                  else
                  {
                    self.$router.push('/')
                  }
                }
                else
                {
                  self.$router.push('/')
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
  },
  created(){
    this.checkUserFun()
    this.getUsersList()
  }
}
</script>

<style scoped>
.usersList{
  text-align: center;
  background-color: rgba(255, 255, 255, 1);
  padding-bottom: 20px;
}
.thTable{
  text-align: center;
}
a{
  font-weight: bold;
}

</style>
