<template>

  <div id="orders">
    Antal 100g: {{countBeef100}},
    Antal 200g: {{countBeef200}},
    Antal Kyckling: {{countChicken}},
    Antal Halloumi: {{countH}}

<div class="grid-d">
    <div>
    <h1>{{ uiLabels.ordersInQueue }}</h1>
    <div>
      <div class="grid-c">
      <OrderItemToPrepare
        v-for="(order, key) in orders"
        v-if="order.status !== 'done'"
        v-on:done="markDone(key)"
        :order-id="key"
        :order="order"
        :ui-labels="uiLabels"
        :lang="lang"
        :key="key">
      </OrderItemToPrepare>
    </div>
    </div>
    </div>
<div>
    <h1>{{ uiLabels.ordersFinished }}</h1>
    <div>

        <div class="griditem" v-for="(order, key) in orders" :key="key">
                <div class="grid-c">
          <OrderItem
            v-if="order.status === 'done'"
            :order-id="key"
            :order="order"
            :lang="lang"
            :ui-labels="uiLabels">
          </OrderItem>
                  </div>

      </div>
    </div>
    </div>
    Här skriva in kritiskt lagersaldo <div id="statusElement">
 <!--  <Ingredient
   ref="ingredient"
   v-bind:class="{ good: true }"
   v-for="item in ingredients"
   v-if="item.stock>=20"
   :item="item"
   :lang="lang"
   :key="item.ingredient_id">
   </Ingredient>
   <Ingredient
   ref="ingredient"
   v-bind:class="{ neutral: true }"
   v-for="item in ingredients"
   v-if="10<item.stock<20"
   :item="item"
   :lang="lang"
   :key="item.ingredient_id">
   </Ingredient>-->
   <Ingredient
   ref="ingredient"
   v-bind:class="{ bad: true }"
   v-for="item in ingredients"
   v-if="item.stock<=20"
   :item="item"
   :lang="lang"
   :key="item.ingredient_id">
   </Ingredient>
   </div>

  </div>
  <div class="stock">
<h3 align="center">Lagerstatus:</h3>

    <div style="display:flex; justify-content:space-evenly;">
        <div v-for= "item in StockItems" class="StockItems">
        <h4>{{item}}</h4>
        </div>

      </div>
  </div>
  </div>
</template>
<script>
import OrderItem from '@/components/OrderItem.vue'
import OrderItemToPrepare from '@/components/OrderItemToPrepare.vue'
import Ingredient from '@/components/Ingredient.vue'

//import methods and data that are shared between ordering and kitchen views
import sharedVueStuff from '@/components/sharedVueStuff.js'

export default {
  name: 'Ordering',
  components: {
    OrderItem,
    OrderItemToPrepare,
    Ingredient
  },
  mixins: [sharedVueStuff], // include stuff that is used in both
                            //the ordering system and the kitchen
  data:

  function(){
    return {
      chosenIngredients: [],
      price: 0
    }

  },
  computed: {
    countBeef100: function(){
      let counter = 0;
      for(let order in this.orders) {
        for(let i = 0; i < this.orders[order].ingredients.length; i +=1){
          console.log(this.orders[order].ingredients[i]);
          if (this.orders[order].ingredients[i].ingredient_id===6) {
            counter +=1;
          }
        }
      }
      return counter;
    },
    countBeef200: function(){
      let counter = 0;
      for(let order in this.orders) {
        for(let i = 0; i < this.orders[order].ingredients.length; i +=1){
          console.log(this.orders[order].ingredients[i]);
          if (this.orders[order].ingredients[i].ingredient_id===7) {
            counter +=1;
          }
        }
      }
      return counter;
    },
    countChicken: function(){
      let counter = 0;
      for(let order in this.orders) {
        for(let i = 0; i < this.orders[order].ingredients.length; i +=1){
          console.log(this.orders[order].ingredients[i]);
          if (this.orders[order].ingredients[i].ingredient_id===8) {
            counter +=1;
          }
        }
      }
      return counter;
    },
    countH: function(){
      let counter = 0;
      for(let order in this.orders) {
        for(let i = 0; i < this.orders[order].ingredients.length; i +=1){
          console.log(this.orders[order].ingredients[i]);
          if (this.orders[order].ingredients[i].ingredient_id===9) {
            counter +=1;
          }
        }
      }
      return counter;
    }

  },
  methods: {
    markDone: function (orderid) {
      this.$store.state.socket.emit("orderDone", orderid);
    }
  }
}
</script>
<style scoped>
	#orders {
    font-size:24pt;
    margin:10px;
  }
  #box1 {
	width:200px;
	height:200px;
	margin:10px;
	padding:10px;
  border-radius: 25px;
  background:#c00;
}

  h1 {
    text-transform: uppercase;
    font-size: 1.4em;
    margin:10px;
  }
  .grid-c{
    display: grid;
    max.width: 600px 200px 200px 200px 200px;
    width:550px;
    height:auto;
    margin:10px;
    padding:10px;
    background: #FBDE4B;
    border-radius: 25px;
  }
  div:empty {
   display: none;
}
  .grid-d{
    display: grid;
    grid-template-columns: 600px 600px;
  }

  #statusElement {
      width: 256px;
      height: 256px;
  }
  .good {
      background-color: #20A000; /* grön */
      color: blue;
  }
  .bad {
      background-color: #D00; /* röd */
      color: red;
  }
  .neutral{
    color: yellow;
  }

  .stock {
    position: fixed;
    bottom: 0;
    margin: 10px;
    width: 95%;}

  .StockItems{
    border: 1px solid black;
    background: green;
    border-radius: 25px;
    flex-grow: 1;
    flex-shrink: 1;
    flex-basis: 0;
    text-align: center;
    margin: 0 10px;
  }

</style>
