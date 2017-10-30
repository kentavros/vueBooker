<template>
  <div class="main">
  
    Hello!
    <button v-on:click="getMonth()">test</button>

  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Main',
  data () {
    return {
      
    }
  },
  methods: {
      getMonth: function(){
        var self = this
        var date = new Date()
        console.log(date.getMonth())
        // console.log(date.getDay())
      }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
