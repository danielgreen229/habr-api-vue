<template>
  <div class="option__container" >
    <div class="option__image" 
      :style="{ 'background-image': `url(${option.avatar ? option.avatar : null})` , 
                'background-color': `${option.avatar ? 'transparent' : 'grey' }`}">
    </div>
      <span class="option__desc">
        <span class="option__title">{{ option.name ? option.name : option.alias }}</span>
        <span class="option__span">{{ option.type == "user" ? '@' + option.alias : 'Компания' }}</span>
      </span>
  </div>
</template>
<script>
export default {
  name: 'options',
  props: {
    option: Object
  },
};
</script>
<style scoped> 
.option__container {
  display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: flex-start;
}
.option__image {
  height: 2em;
  width: 2em;
  border-radius: 0.3em;
  background-repeat: no-repeat;
    background-size: contain;
}
.option__desc {
  display: flex;
  flex-direction: column;
  margin-left: 0.5em;
}
.option__title {
  color: black;
}
.option__span {
  color: #8e8e8e;
}
</style>
