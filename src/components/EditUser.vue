<template>
  <div class="editUser">
EDIT!
    <router-link to="/emplist">
      <button class="btn btn-info" v-on:click="$parent.getUsersList()">Back</button>
    </router-link>



  </div>
</template>

<script>
export default {
  name: 'editUserForm',
  data () {
    return {
      msg: '',
      errorMsg: ''
    }
  }
}
</script>

<style scoped>

</style>
