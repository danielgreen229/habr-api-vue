<template>
  <div class="home">   
   	<searchOptions :searchPlaceholder="propPlaceholder"/>
  </div>
</template>

<script>
import searchOptions from '../components/search.vue'

export default {
  	name: 'Home',
  	components: { searchOptions },
   	data() {
      return {
      	propPlaceholder: 'Поиск'
      }
  	},
}
</script>
