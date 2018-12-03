<template>

  <div id="orders">
<div class="grid-d">
    <div>
    <h1>{{ uiLabels.ordersInQueue }}</h1>
    <div>
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
<div>
    <h1>{{ uiLabels.ordersFinished }}</h1>
    <div>
      <div class="grid-c">
        <div class="griditem" v-for="(order, key) in orders" :key="key">
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
  data: function(){
    return {
      chosenIngredients: [],
      price: 0
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
  }

  h1 {
    text-transform: uppercase;
    font-size: 1.4em;
  }
  .grid-c{
    display: grid;
    grid-template-columns: 200px 200px 200px 200px 200px;
  }
  .grid-d{
    display: grid;
    grid-template-columns: 600px 600px;
  }
</style>
