




<template>
  <body class= "theborder">
    <head>
      <link href="https://fonts.googleapis.com/css?family=Bree+Serif" rel="stylesheet">
    </head>




  <div class="homepage">
  <div class="homepageheader">

      <h2 style="font-family:'Bree Serif', serif">  Welcome to </h2>
      <h1 style="font-family:'Bree Serif', serif"> Crafty Burgers</h1>


      <div class ="upperBorder">
        <button id ="languageButton" v-on:click="switchLang()">{{ uiLabels.language }} </button>
      </div>
      </div>
   <div class="mainBorder">
     <div class="button">
     <button id="CraftButton" href="/ordering">Create your own burger<img src="http://thinkingstiff.com/images/matt.jpg"></button>
     <div class= "bottombox">
       <table>
         <tr>
           <td>


       <div class= "boxwithin">

       </div>
     </td>
     <td>
       <div class ="boxwithin">
       </div>
     </td>
     <td>

       <div class ="boxwithin">
       </div>

     </td>

     <td>

       <div class ="boxwithin">
       </div>

     </td>



     </tr>

   </table>



     </div>
     </div>

     <form>
   <button id="CraftButton" formaction="/#/ordering">Create your own burger</button>
   </form>

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

table {

  border-spacing: 2em .5em;
  border-spacing: 1em .5em;
 padding: 0 2em 1em 0;
 border-radius: 1px;


}

td {
  height: 3.5em;
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
  height: 30%;
  top: 50%;
  right: 20%;
  overflow: hidden;
  border-radius: 8vh;
  background-color: green;
}

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
  }
}




</style>
