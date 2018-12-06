<template>
  <div class="ingredient" :style ="{'background-image': 'url(' + getImage(item.image) +')'}">
    <label>
      <button class="incrementButton" v-on:click="incrementCounter">+</button>
      <button class="decreaseButton" v-on:click="decreaseCounter">-</button>
      <div class="ingredient_head">
      {{item["ingredient_"+ lang]}}, {{item.selling_price}}:-
      </div>
      <div class="ingredient_amount">
        {{counter}}
      </div>
    </label>

  </div>
</template>
<script>
export default {
  name: 'Ingredient',
  props: {
    item: Object,
    lang: String
  },
    data: function () {
    return {
      counter: 0
    };
  },
  methods: {
    incrementCounter: function () {
      if(this.item.stock > this.counter){
      this.counter += 1;
      // sending 'increment' message to parent component or view so that it
      // can catch it with v-on:increment in the component declaration
      this.$emit('increment');
  }
},
getImage:function(image){
  if (image !== "") {
    let img =require('../assets/' + image);
    return img;
  }
  else return require('../assets/nopic.jpg');
},
    decreaseCounter: function(){
      if(this.counter > 0){
      this.counter -= 1;
      this.$emit("decrease");
    }
  },
    resetCounter: function () {
      this.counter = 0;
    }
  }
}
</script>
<style scoped>

.ingredient{
  position:relative;
}

button{
  position:absolute;
}

.incrementButton{
width: 25%;
left: 0;
bottom: 0;
margin-bottom: 1px;
margin-left: 1px;
border-radius: 4px;
background-color: green;
  border-color: black;
}

.decreaseButton{
  width:25%;
  right: 0;
  bottom: 0;
  margin-bottom: 1px;
  margin-right: 1px;
  border-radius: 4px;
  background-color: yellow;
  border-color: black;
}

.ingredient_head{
  text-align: center;
  font-size: 20px;
  color: rgb(0,0,0);
}

.ingredient_amount{
  position:absolute;
  top: 73%;
  margin-left: 43%;
  font-size: 36px;
  color: rgb(0,0,0);
}

</style>
