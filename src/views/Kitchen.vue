<template>

  <div id="orders">
    Antal 100g: {{countBeef100}}

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

//import methods and data that are shared between ordering and kitchen views
import sharedVueStuff from '@/components/sharedVueStuff.js'

export default {
  name: 'Ordering',
  components: {
    OrderItem,
    OrderItemToPrepare
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
          if (this.orders[order].ingredients[i].ingredient_id===2) {
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
    height:70px;
    margin:10px;
    padding:10px;
    background: #FBDE4B;
    border-radius: 25px;
  }
  .grid-d{
    display: grid;
    grid-template-columns: 600px 600px;
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
