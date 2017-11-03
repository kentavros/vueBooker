<template>
  <div class="bookIt">
    Book it
    <!-- id: {{room.id}}<br>
    name: {{room.name}} -->
    {{room}}
    {{user}}
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'BookItForm',
  data () {
    return {
      msg: '',
      errorMsg: '',
      room: {},
      user: {}
    }
  },
  methods:{
    getRoom: function(id){
      var self = this
      axios.get(getUrl() + 'rooms/hash/' + self.user.hash + '/id_user/' + self.user.id +
      '/id/' + id)
            .then(function (response) {
            if (Array.isArray(response.data))
            {
              self.room = response.data
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
        axios.get(getUrl() + 'users/hash/' + self.user.hash + '/id_user/' + self.user.id +
        '/id/' + self.user.id)
            .then(function (response) {
              // console.log(response.data)
              if (Array.isArray(response.data))
              {
                if (self.user.hash === response.data[0].hash)
                {
                  if (response.data[0].role == 'admin' || response.data[0].role == 'user')
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
    }
  },
  created(){
    this.checkUserFun()
    this.getRoom(this.$route.params.id)
  }
}
</script>

<style scoped>

</style>
