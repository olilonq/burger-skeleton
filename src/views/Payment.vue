<template>
  <body>
    <head>
      <link href="https://fonts.googleapis.com/css?family=Bree+Serif" rel="stylesheet">
    </head>

    <div id="left"></div>
    <div id="right"></div>
    <div id="top"></div>
    <div id="bottom"></div>


    <div class="payment">
      <button id ="languageButton" v-on:click="switchLang()">{{ uiLabels.language }} </button>
      <button id ="homeButton">{{ uiLabels.home }} </button>

      <div>
        <h1 style="text-align: center; font-family:'Bree Serif'">{{ uiLabels.payment }}</h1>
      </div>


      <div




    </div>

  </body>
</template>





<script>


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

  #top, #bottom, #left, #right {
  	background: gray;
  	position: fixed;
  	}
  	#left, #right {
  		top: 0; bottom: 0;
  		width: 10px;
  		}
  		#left { left: 0; }
  		#right { right: 0; }

  	#top, #bottom {
  		left: 0; right: 0;
  		height: 10px;
  		}
  		#top { top: 0; }
  		#bottom { bottom: 0; }

  #languageButton {
    position: fixed;
    transition: .5s ease;
    top: 15px;
    right: 15px;
    overflow: hidden;

  }

  #homeButton{
    position: fixed;
    transition: .5s ease;
    top: 15px;
    left: 15px;
    overflow: hidden;
}






</style>
