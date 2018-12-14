<template>
  <div class="homepage">
  <div class=homepageheader>
      <h2>Welcome to</h2>
      <h1> Crafty Burgers</h1>


      <div class ="upperBorder">
        <button id ="languageButton" v-on:click="switchLang()">{{ uiLabels.language }} </button>
      </div>
      </div>
   <div class="mainBorder">
     <div class="button">
     <button id="CraftButton" href="/ordering">Create your own burger</button>
  </div>
  </div>
  </div>
</template>

<script>

import Ingredient from '@/components/Ingredient.vue'
import OrderItem from '@/components/OrderItem.vue'
import sharedVueStuff from '@/components/sharedVueStuff.js'


export default {
  name: 'Ordering',
  components: {
    Ingredient,
    OrderItem
  },
  mixins: [sharedVueStuff], // include stuff that is used in both
                            // the ordering system and the kitchen
  data: function() { //Not that data is a function!
    return {
      chosenIngredients: [],
      price: 0,
      orderNumber: "",
      pageNumber:1,
    }
  },
  created: function () {
    this.$store.state.socket.on('orderNumber', function (data) {
      this.orderNumber = data;
    }.bind(this));
  },
  methods: {
    addToOrder: function (item) {
      this.chosenIngredients.push(item);
      this.price += +item.selling_price;
    },

    removeFromOrder: function(item){
      this.chosenIngredients.splice(item,1);
      this.price -= item.selling_price;
    },
    placeOrder: function () {
      var i,
      //Wrap the order in an object
        order = {
          ingredients: this.chosenIngredients,
          price: this.price
        };
      // make use of socket.io's magic to send the stuff to the kitchen via the server (app.js)
      this.$store.state.socket.emit('order', {order: order});
      //set all counters to 0. Notice the use of $refs
      for (i = 0; i < this.$refs.ingredient.length; i += 1) {
        this.$refs.ingredient[i].resetCounter();
      }
      this.price = 0;
      this.chosenIngredients = [];
    },
    nextPage: function () {
      if(this.pageNumber < 5){
        this.pageNumber +=1
      }
    },

    previousPage: function () {
      if(this.pageNumber > 1){
        this.pageNumber -=1
      }
    }
  }
}

</script>


<style scoped>

.homepageheader{
  color: black;
  font-size: 7vw;
  text-align: center;
  overflow-y: hidden;
}

  #languageButton {
    position: fixed;
    transition: .5s ease;
    top: 0;
    right: 0;
    overflow: hidden;
  }

#CraftButton{
  position: fixed;
  transition: .5s ease;
  font-size: 30px;
  width: 60%;
  height: 30%;
  top: 50%;
  right: 20%;
  overflow: hidden;
  border-radius: 8vh;
  background-color: green;
}


</style>
