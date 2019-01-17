<template>
  <body>
    <head>
      <link href="https://fonts.googleapis.com/css?family=Bree+Serif" rel="stylesheet">
    </head>

    <div id="left"></div>
    <div id="right"></div>
    <div id="top"></div>
    <div id="bottom"></div>

    <form id= "homeButton">
      <input type="image" src="https://thumbs.gfycat.com/BigheartedRepulsiveIndianelephant-small.gif"  width="48" height="48" formaction="/#/">
    </form>


    <div class="payment">
      <button id ="languageButton" v-on:click="switchLang()">{{ uiLabels.language }} </button>


      
      <form id= "paymentButton">
        <div>
          <h3 style="text-align: center; font-family:'Bree Serif'"> {{uiLabels.clickToPay}}  </h3>
        </div>
      </form>

      <div>
        <h3 style="text-align: center; font-family:'Bree Serif'"> {{uiLabels.paymentOrder}} </h3>

        <div class="griditem" v-for="(order, key) in orders" :key="key">
          <div class="grid-c">
          <OrderItem
            v-if="order.status === 'not-started'"
            :order-id="key"
            v-on:paid="markPaid(key)"
            :order="order"
            :lang="lang"
            :ui-labels="uiLabels">
          </OrderItem>
            </div>

      </div>
      </div>



      </div>




  </body>
</template>


<script>

//import the components that are used in the template, the name that you
//use for importing will be used in the template above and also below in
//components
import Ingredient from '@/components/Ingredient.vue'
import OrderItem from '@/components/OrderItem.vue'

//import methods and data that are shared between ordering and kitchen views
import sharedVueStuff from '@/components/sharedVueStuff.js'

/* instead of defining a Vue instance, export default allows the only
necessary Vue instance (found in main.js) to import your data and methods */
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
      burgerCount: 1

    }
  },
  created: function () {
    this.$store.state.socket.on('orderNumber', function (data) {
      this.orderNumber = data;
    }.bind(this));
  },

methods: {
  addBurger: function () {
  for (let i = 0; i < this.chosenIngredients.length; i += 1) {
    if (typeof this.chosenIngredients[i].burgerCount === 'undefined') {
      this.$set(this.chosenIngredients[i], 'burgerCount', this.burgerCount);
    }
  }
  this.burgerCount += 1;
  this.clearIngredients();
},

  markServed: function (orderid) {
  this.$store.state.socket.emit("orderServed", orderid);

  },

  markDone: function (orderid) {
    this.$store.state.socket.emit("orderDone", orderid);
  },

  markPaid: function (orderid) {
    this.$store.state.socket.emit("orderPaid", orderid);

  },

currentBurger: function () {
  return this.chosenIngredients.map(function (item) {
    if (typeof item.burgerCount === 'undefined') {
      return item["ingredient_" + this.lang];
    }
  }.bind(this)).join(' ');
},
burgersInOrder: function () {
  return this.chosenIngredients.map(function (item) {
    if (typeof item.burgerCount !== 'undefined') {
      return item.burgerCount + ": " + item["ingredient_" + this.lang];
    }
  }.bind(this)).join(' ');
},

burgerCounter: function () {
  return this.chosenIngredients.map(function (item) {
    if (typeof item.burgerCount === 'undefined') {
      return item["ingredient_" + this.lang];
    }
  }.bind(this));
},

clearIngredients: function () {
  //set all counters to 0. Notice the use of $refs
  for (let i = 0; i < this.$refs.ingredient.length; i += 1) {
    this.$refs.ingredient[i];
  }
},
    addToOrder: function (item) {
      var counter = 0;
      for (var i = 0;i < this.chosenIngredients.length;i++){
        if(this.chosenIngredients[i] === item){
          counter++;
        }
      }

      if(item.stock > counter){
        this.chosenIngredients.push(item);
        this.price += +item.selling_price;
      }
    },

    removeFromOrder: function(item){
      if(this.price > 0 && this.chosenIngredients.includes(item)){
        for(var i = 0; i < this.chosenIngredients.length;i++){
          if(this.chosenIngredients[i] === item){
            break;
          }
        }
        this.chosenIngredients.splice(i,1);
        this.price -= item.selling_price;
      }
    },
    placeOrder: function () {
      if(this.chosenIngredients.length > 0){

      //Wrap the order in an object
      let order = {

          ingredients: this.chosenIngredients,
          price: this.price
        };
      // make use of socket.io's magic to send the stuff to the kitchen via the server (app.js)
      this.$store.state.socket.emit('order', {order: order});
      //set all counters to 0. Notice the use of $refs
      this.clearIngredients();
      this.price = 0;
      this.chosenIngredients = [];
    }

  },
    nextPage: function () {
      if(this.pageNumber < 5){
        this.pageNumber +=1
      }
    },


    previousPage: function () {
      if(this.pageNumber > 1){
        this.pageNumber -=1
      }}

  }
}
</script>









<style scoped>

.grid-c{
  display: grid;
  max.width: 600px 200px 200px 200px 200px;
  width:300px;
  height:auto;
  margin:10px;
  padding:10px;
  border-radius: 10px;
  position: fixed;
}

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

  #homeButton {

    position: fixed;
    top:15px;


  }







  @media screen and (min-height: 900px) and ( min-width: 700px)  {
    .grid-c{
      width:600px;
      left: 8%;
      height:auto;
      padding:10px;
      border-radius: 10px;
      position: fixed;
    }
}





</style>
