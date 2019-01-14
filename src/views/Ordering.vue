<template>
  <div class="ordering">

    <div class ="upperBorder">
      <button id ="languageButton" v-on:click="switchLang()">{{ uiLabels.language }} </button>
      <form >
        <input type="image" src="https://thumbs.gfycat.com/BigheartedRepulsiveIndianelephant-small.gif"  width="48" height="48" formaction="/#/">
      </form>


      <h1 id="siteTitle"> </h1>
    </div>

    <div class="sidenav">

  <div><a v-on:click= "pageNumber=1"  ><span>

     {{ uiLabels.bread }} </span> </a></div>
  <P class=”twentysixpoint”</P>
  <div><a v-on:click= "pageNumber=2"> <span> {{ uiLabels.protein }} </span></a></div>
  <P class=”twentysixpoint”</P>
  <div><a v-on:click= "pageNumber=3"> <span> {{ uiLabels.vegetable }} </span> </a></div>
  <P class=”twentysixpoint”</P>
  <div><a v-on:click= "pageNumber=4"> <span> {{ uiLabels.other }} </span> </a></div>
  <P class=”twentysixpoint”</P>
  <div><a v-on:click= "pageNumber=5"> <span> {{ uiLabels.sides }} </span> </a></div>

  </div>




  <div class= "img">




    <div >
      <img src="https://img.icons8.com/ios/1600/down.png"  width="30">
      </div>

    </div>

    <div class= "img2">

      <div >
        <img src="https://img.icons8.com/ios/1600/down.png"  width="30">
        </div>
      </div>

      <div class= "img3">

        <div >
          <img src="https://img.icons8.com/ios/1600/down.png"  width="30">
          </div>
        </div>

        <div class= "img4">

          <div >
            <img src="https://img.icons8.com/ios/1600/down.png"  width="30">
            </div>
          </div>



<div class= "v1">

</div>

  <div class="nexttosidenav">

    <div><a >{{ chosenIngredients.map(item => item["ingredient_"+lang]).join(' ') }}</a></div>
    <P class=”twentysixpoint”</P>
    <div><a> </a></div>
    <P class=”twentysixpoint”</P>
    <div><a ></a></div>
    <P class=”twentysixpoint”</P>
    <div><a> </a></div>
    <P class=”twentysixpoint”</P>
    <div><a ></a></div>

  </div>
  <div id="ingredientHeader">
    <h1  v-if = "pageNumber===1">{{ uiLabels.bread }}</h1>
    <h1  v-if = "pageNumber===2">{{ uiLabels.protein }}</h1>
    <h1  v-if = "pageNumber===3">{{ uiLabels.vegetable }}</h1>
    <h1  v-if = "pageNumber===4">{{ uiLabels.other }}</h1>
    <h1  v-if = "pageNumber===5">{{ uiLabels.sides }}</h1>

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

      <h2 id="currentOrder">{{ uiLabels.order }}</h2>
      {{ burgersInOrder() }}
    <hr>
    {{ currentBurger() }}, {{ price }} kr
    <button v-on:click="addBurger()">{{ uiLabels.addToOrder }}</button>
    <button v-on:click="placeOrder()">{{ uiLabels.placeOrder }}</button>
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
currentBurger: function () {
  return this.chosenIngredients.map(function (item) {
    if (typeof item.burgerCount === 'undefined') {
      return item["ingredient_" + this.lang];
    }
  }.bind(this)).join(', ');
},
burgersInOrder: function () {
  return this.chosenIngredients.map(function (item) {
    if (typeof item.burgerCount !== 'undefined') {
      return item.burgerCount + ": " + item["ingredient_" + this.lang];
    }
  }.bind(this)).join(', ');
},
clearIngredients: function () {
  //set all counters to 0. Notice the use of $refs
  for (let i = 0; i < this.$refs.ingredient.length; i += 1) {
    this.$refs.ingredient[i].resetCounter();
  }
},
    addToOrder: function (item) {
      this.chosenIngredients.push(item);
      this.price += +item.selling_price;
    },

    removeFromOrder: function(item){
      this.chosenIngredients.splice(item,1);
      this.price -= item.selling_price;
    },
    placeOrder: function () {

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
/* scoped in the style tag means that these rules will only apply to elements, classes and ids in this template and no other templates. */
#siteTitle {
  display: flex;
  align-items:center;
  justify-content: center;
  color:black;
}

.img {
  position: fixed;
  left: 2em;
  top: 11.9em;
  z-index: -1;
}

.img2 {
  position: fixed;
  left: 2em;
  top: 16.5em;
  z-index: -1;

}

.img3 {
  position: fixed;
  left: 2em;
  top: 21.1em;
  z-index: -1;
}

.img4 {
  position: fixed;
  left: 2em;
  top: 25.7em;
  z-index: -2;
}

.v1 {
  border-left: 1px solid gray;
  height: 700px;

  position: absolute;
  left: 11em;
  top: 5em;
  z-index: -2;
}



.ordering {
  display:grid;
  position: flex;


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
  position: fixed;
  margin-top: 35%;
  margin-left: 50%;
  height: 40%;
  width: 40%;

  display:grid;
  grid-template-columns: auto auto;

  overflow-y: scroll;
  overflow-x: hidden;

}


.bottomBorder {
  background-color: orange;
  width: 100%;
  height: 25%;
  border-color:black;
  border-top: 1px solid #ccd;
  position: fixed;
  bottom:0;
  left:0;

}
#homeButton {
  position: absolute;
  transition: .5s ease;
  top: 0;
  left: 0;
  z-index:2;
  height: 50%;
  width: 12%;

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
  margin-left: 0.5em;
  margin-bottom:0.5em;
  color: black;
  background-size: 8em 8em;

}
#ingredientHeader {
  align-items:center;
  justify-content: center;

  position: fixed;
  z-index:2;
  top:12%;
  right: 17%;

}
#currentOrder {
  display: flex;
  align-items:center;
  justify-content: center;
  color:black;

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
  position: fixed;
  margin-top: 35%;

    width: 80px;
    z-index: 0;
    top: 10px;
    padding: 2px 1;
}


.nexttosidenav{

  position: fixed;
  margin-top: 35%;

    z-index: -1;
    top: 10px;
    padding: 2px 1;

}

.nexttosidenav div {
  border: 1px solid black;
  display: flex;
  align-items:center;
  justify-content: center;
  width: 5em;
  height: 3.5em;
  border-radius: 25px;
  background-color: #FF8C00;
  margin-left: 80px;

}

.sidenav div {
  border: 1px solid black;

  display: flex;

  align-items:center;
  justify-content: center;
  width: 3.5em;
  height: 3.5em;
  margin-left: 10px;
  border-radius: 50px;
  background-color: #FF8C00;

}


.sidenav span{
  color:black;

}

.sidenav a:hover span{

  font-weight: bold;
  cursor: pointer;
}



.sidenav a:hover {
    color: #064579;


}

.main {
    margin-left: 320px; /* Same width as the sidebar + left position in px */
    font-size: 28px; /* Increased text to enable scrolling */
    padding: 1px 10px;
}
@media screen and (min-height: 900px) and ( min-width: 700px) {
  #ingredientHeader {
    top:14%;
    right: 27%;
  }
  .ingredient {
    width: 15em;
    height: 15em;
    background-size: 15em 15em;

  }
  .middlepanel {

    margin-left: 33%;
    margin-top: 25%;
    height: 48.5%;
    width: 65%;

  }
  .sidenav {
    margin-top: 25%;

    width: 10em;
    }
  .sidenav div {

    width: 5em;
    height: 5em;
  }
  .nexttosidenav {
    width: 10em;
    margin-top: 25%;

  }
  .nexttosidenav div {
    width: 8em;
    height: 5em;
    margin-left: 7em;
  }
}
@media screen and (max-width: 500px) {
  .middlepanel {
  grid-template-columns: auto;

}

}
</style>
