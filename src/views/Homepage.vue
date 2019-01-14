




<template>
  <body class= "theborder">
    <head>
      <link href="https://fonts.googleapis.com/css?family=Bree+Serif" rel="stylesheet">
    </head>




  <div class="homepage">
  <div class="homepageheader">

      <h2 style="font-family:'Bree Serif', serif">  Welcome to </h2>
      <h1 style="font-family:'Bree Serif', serif"> Crafty Burgers</h1>

      <div class="fastorderpanel">
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


      <div class ="upperBorder">
        <button id ="languageButton" v-on:click="switchLang()">{{ uiLabels.language }} </button>
      </div>
      </div>
   <div class="mainBorder">
     <div class="button">
     <button id="CraftButton" href="/ordering">Create your own burger<img src="http://thinkingstiff.com/images/matt.jpg"></button>
     <button id= "fastorderbutton" v-on:click="placeOrder()">{{ uiLabels.placeOrder }}</button>

     <form>
   <button id="CraftButton" formaction="/#/ordering">Create your own burger</button>
   </form>

  </div>
  </div>

  </div>

  </body>
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
      pageNumber:6,
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

table {

  border-spacing: 2em .5em;
  border-spacing: 1em .5em;
 padding: 0 2em 1em 0;
 border-radius: 1px;


}

#fastorderbutton{

  position: relative;
  margin-top: 93%;
  margin-right:45%;
  margin-left: 55%;
  height: 40%;
  width: 30%;




}




.fastorderpanel {
  position: fixed;
  margin-top: 45%;
  margin-right:45%;
  margin-left: 5%;
  height: 40%;
  width: 30%;

  display:grid;
  grid-template-columns: auto auto;


}

.ingredient {
  border: 1px solid #ccd;
  border-radius:10px;
  width: 5em;
  height: 5em;
  margin-left: 0.5em;
  margin-bottom:0.5em;
  color: black;
  background-size: 5em 5em;

}
#ingredientHeader {
  align-items:center;
  justify-content: center;

  position: fixed;
  z-index:2;
  top:12%;
  right: 17%;

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

<<<<<<< HEAD
.bottombox {
  position: fixed;
  top: 84%;
  right: 20%;
  left: 10%;
  width: 80%;
  height: 15%;
  border: 1px solid gray;
  border-radius: 25px;

}

.boxwithin {

  position:relative;
  top: 20%;
  right:15%;
  width: 55px;
  height: 55px;
  border: 1px solid gray;
  border-radius: 15px;


=======
>>>>>>> f10701dda3f32c6e2a2023e477d62f9709af0c5f





<<<<<<< HEAD
}

.theborder {
  position: relative;
  border:  5px solid gray;



}
@media screen and (min-height: 900px) and ( min-width: 700px) {
  .homepageheader {
    font-size: 6vw;
  }
  #languageButton {
    top: 1.2%;
    right: 2.5%;

  }
  .boxwithin {
    width: 110px;
    height: 110px;
    right: 10%;
    left:40%
=======
>>>>>>> f10701dda3f32c6e2a2023e477d62f9709af0c5f

  }
}




</style>
