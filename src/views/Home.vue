<template>
  <div class="home">   
   	<div>
		<multiselect 
		 	v-model="value" 
		 	:options="countries" 
		 	placeholder="Select one" 
		 	:custom-label="nameWithLang"
		 	:searchable="true" 
		  	:loading="isLoading" 
		  	:internal-search="false" 
		  	:clear-on-search="true"
		  	:clear-on-select="true" 
		  	:close-on-select="true" 
		  	:options-limit="300" 
		  	:limit="3" 
		 	track-by="alias"
		 	:max-height="220" 
		  	:show-no-results="true" 
		  	:hide-selected="true" 
		  	@search-change="asyncFind"
		  	selectLabel=""
		  	:multiple="true"
		  	@select="selectOption(value)"
		>

	  		<template slot="option" slot-scope="props">
	  			<searchOption :option="props.option"/>
	  		</template>

	  		<span slot="noResult">Oops! No elements found. Consider changing the search query.</span>
		</multiselect>


	
	</div>


  </div>

</template>

<script>
import axios from 'axios'
import Multiselect from 'vue-multiselect'
import 'vue-multiselect/dist/vue-multiselect.min.css'
import searchOption from '../components/option.vue'

export default {
  	name: 'Home',
  	components: { Multiselect, searchOption },
   	data() {
      return {
       	value: null,
       	selectedCountries: [],
        countries: [],
        isLoading: false
      }
  	},
  	methods: {
  		nameWithLang ({ name, alias }) {
	      	return  alias ? '@' + alias : name
	    },
   		clearSelected () {
   			this.selectedCountries = []
   		},
	    asyncFind (query) {
	      if(query.length > 2) {
	      	this.isLoading = true
	      	axios.get("https://habr.com/kek/v2/publication/suggest-mention?q=" + query)
          	.then(res => {
          		this.countries = res.data.data
          		this.isLoading = false
          	})
          	.catch(err => console.log(err));
	      }	
	    },
	    selectOption (value) {
	    	if(this.value != null && this.value.length > 0) {
	    		this.value[0] = this.value[1]
	    		this.value.pop()
	    	}
	    },
	    clearAll () {
	      this.selectedCountries = []
	    }
   	},
   	created() {
   		this.asyncFind('aaa')
   	}
 
}
</script>
<style scoped>
>>>.multiselect__tag {
	background: #91a8ba;
	margin-bottom: 0px;
}
>>>.multiselect__tag-icon:after {
	color: white;
}
>>>.multiselect__tag-icon {
	background-color: transparent;
}
>>>.option__single-label {
	color: white;
}
>>>.multiselect__option--highlight {
	background-color: #eeeeee;
}
</style>