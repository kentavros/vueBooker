<template>
  <div class="empList">
    <div class="usersList">
      <div id="legend">
            <legend class="title">Employee List</legend>
      </div>
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
         <tbody v-for="(u, index) in users">
           <tr>
             <td>{{u.id}}</td>
             <td>{{u.login}}</td>
             <td>{{u.email}}</td>
             <td>{{u.role}}</td>
             <td><button class="btn alert-info" v-on:click="test(index)">Edit</button></td>
             <td><button class="btn alert-danger" v-on:click="test(index)">Remove</button></td>
           </tr>
         </tbody>
       </table>
       <button class="btn alert-success" v-on:click="1">Add a new Employee</button>
       <router-link to="/"><button class="btn alert-info">Back</button></router-link>
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
    getUsersList:function(){
      var self = this
      axios.get(getUrl() + 'users/hash/' + self.user.hash + '/id_user/' + self.user.id)
          .then(function (response) {
          console.log(response.data)
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
  background-color: rgba(255, 255, 255, 0.8);
  padding-bottom: 20px;
}
.thTable{
  text-align: center;
}
</style>
