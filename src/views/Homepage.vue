
<template>
  <body >
    <head>
      <link href="https://fonts.googleapis.com/css?family=Bree+Serif" rel="stylesheet">
    </head>


  <div class="homepage">

  <div class="homepageheader">

      <h2 style="font-family:'Bree Serif', serif">  {{uiLabels.welcome}} </h2>
      <h1 style="font-family:'Bree Serif', serif"> Crafty Burgers </h1>

      <div class ="upperBorder">
        <button id ="languageButton" v-on:click="switchLang()"> {{ uiLabels.language }} </button>
      </div>
    </div>


   <div class="mainBorder">
     <div class="button">
     <button id="CraftButton" href="/ordering" > {{uiLabels.CreateBurger}} <img src="http://thinkingstiff.com/images/matt.jpg"></button>
</div>
     <form>
   <button id="CraftButton" formaction="/#/ordering" style="font-family:'Bree Serif', serif"> {{uiLabels.CreateBurger}} </button>
   </form>
</div>

<div class= "fastorderingheader">


</div>


   <div class="fastorderpanel">

     <Ingredient
     v-if ="item.category===pageNumber"
     ref="ingredient"
     v-for="item in ingredients"
     v-on:increment="addToOrder(item)"
     v-on:decrease="removeFromOrder(item)"
     :item="item"
     :key="item.ingredient_id">
     </Ingredient>
     </div>

  </div>




     <form>

       <button id= "fastorderbutton" v-on:click="placeOrder()"  formaction="/#/Payment" style="box-shadow: 0 8px 16px 0 rgba(0,0,0,0.2), 0 6px 20px 0 rgba(0,0,0,0.19) "> {{ uiLabels.placeOrder }}</button>

     </form>


  </body>
</template>





<script>

import Ingredient from '@/components/Ingredient.vue'
import OrderItem from '@/components/OrderItem.vue'
import sharedVueStuff from '@/components/sharedVueStuff.js'


export default {
  name: 'Homepage',
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
      pageNumber:6,
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
      this.chosenIngredients.push(item);
      this.price += +item.selling_price;
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
        this.$refs.ingredient[i];
      }
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
      }
    }
  }


}

</script>



<style scoped>

.fastorderingheader{

  align-items:center;
  justify-content: center;

  position: fixed;
  z-index:2;
  top:68%;
  right: 70%;





}






#fastorderbutton{

  position: fixed;
  top:92%;
  left:37.5%;
  height: 5%;
  width: 25%;


}

.fastorderpanel {
  position: absolute;
  margin-right:45%;
  margin-left: 20%;
  height: 30%;
  width: 30%;
  top: 75%;
  display:grid;
  grid-template-columns: auto auto;


}

.ingredient {
  border: 1px solid #ccd;
  border-radius:10px;
  width: 6em;
  height: 6em;
  margin-right: 0.5em;
  margin-bottom:0.5em;
  color: black;
  background-size: 6.5em 6em;

}


.homepageheader{
  color: black;
  font-size: 7vw;
  text-align: center;
  overflow-y: hidden;
}

  #languageButton {
    position: fixed;
    transition: .5s ease;
    top: 1.8%;
    right: 5%;
    overflow: hidden;
  }

#CraftButton{
  position: fixed;
  transition: .5s ease;
  font-size: 30px;
  width: 60%;
  height: 25%;
  top: 44%;
  right: 20%;
  overflow: hidden;
  border-radius: 8vh;
  background-color: green;
}





@media screen and (min-height: 900px) and ( min-width: 700px) {
  .homepageheader {
    font-size: 6vw;
  }
  #languageButton {
    top: 1.2%;
    right: 2.5%;


}


.ingredient {
  left:26%;
  width: 10em;
  height: 10em;
  background-size: 10em 10em;

}


}






</style>
