<template>
  <div class="ordering">
    <img class="example-panel" src="@/assets/exampleImage.jpg">

    <div class ="upperBorder">
      <button class ="languageButton" v-on:click="switchLang()">{{ uiLabels.language }} </button>
      <h1>{{ uiLabels.ingredients }}  </h1>
    </div>
    <div class ="leftMiddlepanel">

    </div>
    <div class="middlepanel">
      <Ingredient
      v-if ="item.category===pageNumber"
      ref="ingredient"
      v-for="item in ingredients"
      v-on:increment="addToOrder(item)"
      v-on:decrease="removeFromOrder(item)"
      :item="item"
      :lang="lang"
      :key="item.ingredient_id">
      </Ingredient>
      </div>
      <div class="sidenav">

  <div><a href="#Bröd">Bröd</a></div>
  <a href="#Protein">Protein</a>
  <a href="#Grönsak">Grönsak</a>
  <a href="#Övrigt">Övrigt</a>
  <a href="#Tillbehör">Tillbehör</a>
</div>
    <div class="bottomBorder">

      <h1>{{ uiLabels.order }}</h1>
      {{ chosenIngredients.map(item => item["ingredient_"+lang]).join(', ') }}, {{ price }} kr
      <button v-on:click="placeOrder()">{{ uiLabels.placeOrder }}</button>
      <button v-on:click="nextPage()">{{ uiLabels.next }}</button>
      <button v-on:click="previousPage()">Back</button>

      <h1>{{ uiLabels.ordersInQueue }}</h1>
      <OrderItem
        v-for="(order, key) in orders"
        v-if="order.status !== 'done'"
        :order-id="key"
        :order="order"
        :ui-labels="uiLabels"
        :lang="lang"
        :key="key">
      </OrderItem>
    </div>
  </div>
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
/* scoped in the style tag means that these rules will only apply to elements, classes and ids in this template and no other templates. */

.ordering {
  width: 30em;
  height: 50em;
  grid-auto-rows: 10em;
  grid-auto-columns: 10em;
  display:grid;
  position: relative;
  margin:auto;

}
.example-panel {
  position: fixed;
  left:1;
  top:0;
  z-index: -2;

}

.upperBorder {
  position:fixed;
  grid-column-start:1;
  grid-column-end:4;
  grid-row-start:1;
  grid-row-end:2;
  background-color: orange;
  width: 31em;
  height: 10em;
  border-color:black;
  border: 1px solid #ccd;
  top:1;
  }
.leftMiddlepanel {

  grid-column-start:1;
  grid-column-end:1;
}
.middlepanel {
  grid-column-start:2;
  grid-column-end:4;
  grid-row-start: 2;
  grid-row-end:5;
  display: grid;
  grid-template-columns: 10em 10em;
  grid-row-end:5;

}
.bottomBorder {
  grid-column-start:1;
  grid-column-end:3;
  grid-row-start:4;
  grid-row-end:5;
  background-color: orange;
  width: 31em;
  height: 10em;
  border-color:black;
  border: 1px solid #ccd;
  position: fixed;
  bottom:0;

}

.ingredient {

  border: 1px solid #ccd;
  border-radius:10px;
  padding: 1em;
  margin-right:0.5em;
  margin-left: 0.5em;
  margin-top: 0.5em;
  color: white;
  width:7em;
  height: 7em;
  background-size: 150px 150px;
}

.sidenav {
    width: 130px;
    position: fixed;
    z-index: 1;
    top: 30px;
    left: 10px;
    background: #fbe2a4;
    overflow-x: hidden;
    padding: 8px 1;
}

.sidenav div {
  display: flex;
  align-items:center;
  justify-content: center;
  width: 100px;
  height: 100px;
  border-radius: 50px;
  background-color: #FF8C00;
}

/* .sidenav a {
    padding: 100px 80px 6px 16px;
    text-decoration: none;
    font-size: 25px;
    color: black;
    display: block;
    height: 15px;
    width: 25px;
    background-color: #FF8C00;
    border-radius: 200%;

  } */




.sidenav a:hover {
    color: #064579;
}

.main {
    margin-left: 240px; /* Same width as the sidebar + left position in px */
    font-size: 28px; /* Increased text to enable scrolling */
    padding: 1px 10px;
}

@media screen and (max-height: 450px) {
    .sidenav {padding-top: 15px;}
    .sidenav a {font-size: 18px;}
}
</style>
