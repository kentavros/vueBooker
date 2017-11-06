<template>
  <transition name="modal">
    <div class="modal-mask">
      <div class="modal-wrapper">
        <div class="modal-container">
          <div class="btnclose">
          <button class="btn-danger btn-xs" type="button" v-on:click="$emit('close')" >X</button>
          </div>
            <h4>B.B. DETAILS</h4>
            <table class="table table-hover table-bordered">
              <tbody>
                <tr>
                  <th>
                    When:
                  </th>
                  <td>
                      Input time
                  </td>
                </tr>
                <tr>
                  <th>
                    Notes:
                  </th>
                  <td>
                    {{event.description}}
                  </td>
                </tr>
                <tr>
                  <th>
                    Who:
                  </th>
                  <td v-if="event.user_name">
                    {{event.user_name}}
                  </td>
                  <td v-else class="alert-danger">
                    Еhe user has been removed
                  </td>
                </tr>
                <tr>
                  <td colspan="2">
                    Submitted: {{event.create_time}}
                  </td>
                </tr>
              </tbody>
            </table>
          <div v-if="occurrenceSection == 'show'" class="checkA">
              <input type="checkbox" id="checkbox" v-model="checked">
              <label for="checkbox">Apply to all occurrences?</label>
          </div>
          <div v-if="access == '2' || user.id == event.id_user" class="btn-section">
            <button class="btn btn-primary">Update</button>
            <button class="btn btn-danger">Delete</button>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>
<script>
import axios from 'axios'
export default {
  name: 'HelloWorld',
  props: ['sentEvent', 'sentUser'],
  data () {
    return {
      msg: '',
      errorMsg: '',
      access: '',
      eventParent: '',
      event: {},
      events:[],
      user: {},
      users: [],
      occurrenceSection: '',
      checked: ''
    }
  },
  methods: {
    setProporties: function(){
      var self = this
      self.user = self.sentUser
      self.event = self.sentEvent
      self.checkUserRole()
      self.getUsersList()
      if (self.access == '2' || self.user.id == self.event.id_user)
      {
        self.getEventsIdParent()
      }
    },
    checkUserRole: function(){
      var self = this
      if (self.user.role == 'admin')
      {
        self.access = '2'
      }
      else{
        self.access = '1'
      }
    },
    getUsersList: function(){
      var self = this
      if (self.user.role != 'admin')
      {
        return false
      }
      axios.get(getUrl() + 'users/hash/' + self.user.hash + '/id_user/' + self.user.id)
        .then(function (response) {
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
    getEventsIdParent: function(){
      var self = this
      self.errorMsg = ''
      self.events = []
      var requestUrl = ''
      if (self.event.id_parent)
      {
        requestUrl = getUrl() + 'events/hash/' + self.user.hash + '/id_user/' + self.user.id + 
      '/flag/parent/id/' + self.event.id_parent 
      }
      else
      {
        requestUrl = getUrl() + 'events/hash/' + self.user.hash + '/id_user/' + self.user.id + 
      '/flag/parent/id/' + self.event.id
      }
      axios.get(requestUrl)
        .then(function (response) {
          console.log(response.data)
        if (Array.isArray(response.data))
        {
          self.events = response.data 
          if (self.events.length > 1)
          {
            self.occurrenceSection = 'show'
          }
        }
        else
        {
          self.errorMsg = response.date
        }
        })
        .catch(function (error) {
          console.log(error)
        })
    }
  },
  created(){
    this.setProporties()
  }
}
</script>

<style scoped>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, .5);
  display: table;
  transition: opacity .3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 300px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, .33);
  transition: all .3s ease;
}
.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
.btnclose{
  float: right;
}
.btn-section{
  text-align: center;
}
.btn-section .btn-danger{
  margin-left: 20px;
}
h4{
  text-align: center;
}
th{
  width: 50px;
}
.checkA{
  text-align: center;
  margin-bottom: 10px;
}
</style>
