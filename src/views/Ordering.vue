<template>
  <div class="ordering">
    <img class="example-panel" src="@/assets/exampleImage.jpg">

    <div class ="upperBorder">
      <button id ="languageButton" v-on:click="switchLang()">{{ uiLabels.language }} </button>
      <h1>{{ uiLabels.ingredients }}  </h1>
    </div>


    <div class="sidenav">

  <div><a href="#Bröd" v-on:click= "pageNumber===1"  >Bröd</a></div>
  <div><a href="#Protein"  v-on:click= "pageNumber===2"     >Protein</a></div>
  <div><a href="#Grönsak" v-on:click= "pageNumber===3"        >Grönsak</a></div>
  <div><a href="#Övrigt">Övrigt</a></div>
  <div><a href="#Tillbehör">Tillbehör</a></div>

  </div>


  <div class="nexttosidenav">


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
    <div class="bottomBorder">

      <h2>{{ uiLabels.order }}</h2>
      {{ chosenIngredients.map(item => item["ingredient_"+lang]).join(', ') }}, {{ price }} kr
      <button id="orderButton" v-if="pageNumber===5" v-on:click="placeOrder()">{{ uiLabels.placeOrder }}</button>
      <button id="nextButton" v-if="pageNumber<5" v-on:click="nextPage()">{{ uiLabels.next }}</button>
      <button id="backButton" v-on:click="previousPage()">Back</button>

      <h2>{{ uiLabels.ordersInQueue }}</h2>
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
  display:grid;
  grid-auto-rows: 8.8em;
  grid-auto-columns: 9em;
  position: relative;


}
.example-panel {
   position: fixed;
   left:0;
   top:0;
   z-index: -2;

 }

.upperBorder {
  position:fixed;
  background-color: orange;
  width: 100%;
  height: 15%;
  border-color:black;
  border-bottom: 1px solid #ccd;
  top:0;
  left:0;
  }

.middlepanel {
  position:relative;
  grid-column: 3;
  grid-column-start:2;
  grid-column-end:4;
  grid-row-start: 2;
  grid-row-end:4;
  height: 100%;
  display: grid;
  grid-template-columns: auto auto;
  grid-column-gap:auto;
  overflow-y: scroll;
  overflow-x: hidden;

}
.bottomBorder {

  background-color: orange;
  width: 100%;
  height: 20%;
  border-color:black;
  border-top: 1px solid #ccd;
  position: fixed;
  bottom:0;
  left:0;

}
#languageButton {
  position: absolute;
  transition: .5s ease;
  top: 0;
  right: 0;

}
#orderButton {
  position: absolute;
  transition: .5s ease;
  top: 0;
  right: 0;
  background-color: rgb(0,200,0);
}

#nextButton {
  position: absolute;
  transition: .5s ease;
  top: 0;
  right: 0;
  background-color: rgb(0,200,0);
}

#backButton {
  position: absolute;
  transition: .5s ease;
  top: 0;
  left: 0;
  z-index: 1;
  background-color: yellow;
}

.ingredient {
  border: 1px solid #ccd;
  border-radius:10px;
  width: 8em;
  height: 8em;
  margin-top: 0.5em;
  margin-left: 0.5em;
  color: black;
  background-size: 130px 130px;

}

#grid {

  display: grid;
  height: 100px;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: 100px;
  column-gap: 10px;

}

#grid > div{
  border: 1px solid green;
  background-color: lime;
}



.sidenav {

  grid-row: 2;
  grid-column: 1 / 2;
  grid-column-end:1;
  grid-row-strt:1;
  grid-row-end: 5;

    width: 80px;
    position: relative;
    z-index: 0;
    top: 10px;
    left: 5px;
    overflow-x: hidden;
    padding: 8px 1;
}


.nexttosidenav{
  display: flex;

  grid-column: 2;
  grid-row: 2;
  background: #FF8C00;

  border-radius: 25px;
  position: relative;
  top: 10px;
  width: 80px;
  height: 50px;
  right:75px;

}

.sidenav div {
  grid-row:2;

  display: flex;

  align-items:center;
  justify-content: center;
  width: 60px;
  height: 60px;
  border-radius: 50px;
  background-color: #FF8C00;

}


.sidenav a:hover {
    color: #064579;

}

.main {
    margin-left: 320px; /* Same width as the sidebar + left position in px */
    font-size: 28px; /* Increased text to enable scrolling */
    padding: 1px 10px;
}
@media screen and (max-height: 450px) {
    .sidenav {padding-top: 15px;}
    .sidenav a {font-size: 18px;}
}
</style>
