<template>
  <div class="ingredient">
    <label>
      <button v-on:click="incrementCounter">+</button>
      <button v-on:click="decreaseCounter">-</button>
      {{counter}}, {{item["ingredient_"+ lang]}}, {{item.selling_price}}:-, {{item.stock}} pcs
      <img :src="getImage(item.image)">
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
  if (image !== undefined) {
    let img =require('../assets/' + image);
    console.log(img);
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

</style>
