<template>
  	<div class="searcher">   
  		<div class="change-multiselect-button__container">
  			<button ref="multiselectButton" class="change-multiselect__button" @click="multipleSelect = !multipleSelect">{{textSelectButton}}</button>
  		</div>
		<multiselect 
			ref="multiselect"
		 	v-model="value" 
		 	:options="options" 
		 	:placeholder="searchPlaceholder"
		 	:custom-label="nameWithLang"
		 	:searchable="true" 
		  	:loading="isLoading" 
		  	:internal-search="false" 
		  	:clear-on-search="true"
		  	:clear-on-select="true" 
		  	:close-on-select="true" 
		  	:options-limit="300" 
		  	:limit="10" 
		  	:limit-text="limitText"
		 	track-by="alias"
		 	:max-height="220" 
		  	:show-no-results="true" 
		  	:hide-selected="true" 
		  	@search-change="asyncFind"
		  	selectLabel=""
		  	:multiple="true"
		  	@select="selectOption(value)"
		  	noOptions="Начните поиск"
		  	:open="false"
		>
	  		<template slot="option" slot-scope="props">
	  			<searchOption :option="props.option"/>
	  		</template>
	  		<span slot="noResult">Упс! По вашему запросу ничего не найдено!</span>
		</multiselect>	
		<transition name="slide-fade">
			<div v-if="error.length > 0" class="search-error__container"> 
				{{error}}
			</div>
		</transition>
  	</div>
</template>

<script>
import axios from 'axios'
import Multiselect from 'vue-multiselect'
import 'vue-multiselect/dist/vue-multiselect.min.css'
import searchOption from '../components/option.vue'

export default {
  	name: 'searcher',
  	props: {
  		searchPlaceholder: ''
  	},
  	components: { Multiselect, searchOption },
   	data() {
      return {
       	value: null,
        options: [],
        isLoading: false,
        error: '',
        multipleSelect: true,
      }
  	},
  	computed: {
  		textSelectButton () {
  			return !this.multipleSelect ? 'Множественный выбор' : 'Одиночный выбор'
  		},
  	},
  	watch: {
    	error () {		
    		setTimeout (() => {
	   			this.error = ''
	   		}, 3000)
    	},
    	multipleSelect () {
    		this.value = null
    	},
    	options () {
    		if(this.options === null || this.options.length == 0) {
    			this.asyncFind('aaa')
    		}
    	}
	},
  	methods: {
  		limitText (count) {
	      return `и ${count} других пользователей`
	    },
  		nameWithLang ({ name, alias }) {
	      	return  alias ? '@' + alias : name
	    },
	    asyncFind (query) {
	      if(query.length > 2) {
	      	this.isLoading = true
	      	axios.get(process.env.VUE_APP_HABR_API + "?q=" + query)
          	.then(res => {
          		this.options = res.data.data
          		this.isLoading = false
          	})
          	.catch((error) => { this.error = 'Ошибка поиска! Повторите попытку позже!'; this.isLoading = false; })
	      }	
	    },
	    selectOption (value) {
	    	if(this.value != null && this.value.length > 0 && !this.multipleSelect) {
	    		this.value[0] = this.value[1]
	    		this.value.pop()
	    	}
	    },
	    onKeyDown(e) {
            if(e.code == "Enter") {
            	this.$refs.multiselect.isOpen = true
            	setTimeout(() => {
            		let className = 'multiselect__input';
            		document.getElementsByClassName(className)[0].focus();
            	}, 500)
            }
            if(e.code == "Escape") {
            	this.$refs.multiselectButton.focus()
            }
        }
   	},
   	created() {
   		this.asyncFind('aaa')
   		document.addEventListener('keydown', this.onKeyDown)
   	},
   	beforeDestroy() {
        document.removeEventListener('keydown', this.onKeyDown)
    },
}
</script>
<style scoped>
.change-multiselect-button__container {
	display: flex;
	justify-content: flex-start;
}
.change-multiselect__button {
	text-decoration: none;
	color: white;
	background-color: #9989f1db;
	border: none;
	border-radius: 0.5em;
	padding: 0.8em 3em;
    width: 17em;
	margin-bottom: 8px;
	cursor: pointer;
}
.search-error__container {
	color: white;
    background-color: #f1a689;
    border-radius: 0.3em;
    text-align: left;
    box-sizing: border-box;
    padding: 0.5em;
    margin-top: 14.5em;
    position: fixed;
    width: calc(100% - 16px);
}
.slide-fade-enter-active {
  	transition: all .3s ease;
}
.slide-fade-leave-active {
  	transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-to {
  	transform: translateY(-1em);
  	opacity: 0;
}
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