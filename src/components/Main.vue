<template>
  <div class="main">
  
    Hello - Main!
    <calendar :weekDays="'sun'" :nameMonth="'en'"></calendar>
  

  </div>
</template>

<script>
import axios from 'axios'
import Calendar from './Calendar'
export default {
  name: 'Main',
  data () {
    return {
      
    }
  },
  components: {
    'Calendar' : Calendar
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
