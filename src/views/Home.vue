<template>
  <div class="home">  
    <turn-table/>
  </div>
</template>

<script>
// @ is an alias to /src
import turnTable from '@/components/turnTable.vue'

export default {
  name: 'home',
  components: {
    turnTable
  }
}
</script>
