<template>
  <div class="ordering">

    <div class ="upperBorder">

      <button id ="languageButton" v-on:click="switchLang()">{{ uiLabels.language }} </button>
      <form >
        <input type="image" src="https://thumbs.gfycat.com/BigheartedRepulsiveIndianelephant-small.gif"  width="48" height="48" formaction="/#/">
      </form>


      <h1 id="siteTitle"> Crafty Burgers</h1>
    </div>

    <div class="sidenav">

  <div class ="sidenav a" v-if ="pageNumber===1"><a >{{ uiLabels.bread }}</a></div>
  <div><a v-on:click= "pageNumber=1"  ><span>

     {{ uiLabels.bread }} </span> </a></div>

  <P class=”twentysixpoint”></P>
  <div class ="sidenav b" v-if ="pageNumber===2"><a >{{ uiLabels.protein }}</a></div>
  <div><a v-on:click= "pageNumber=2"> <span> {{ uiLabels.protein }} </span></a></div>
  <P class=”twentysixpoint”></P>
  <div class ="sidenav c" v-if ="pageNumber===3"><a >{{ uiLabels.vegetable }}</a></div>

  <div><a v-on:click= "pageNumber=3"> <span> {{ uiLabels.vegetable }} </span> </a></div>
  <P class=”twentysixpoint”></P>
  <div class ="sidenav d" v-if ="pageNumber===4"><a >{{ uiLabels.other }}</a></div>

  <div><a v-on:click= "pageNumber=4"> <span> {{ uiLabels.other }} </span> </a></div>
  <P class=”twentysixpoint”></P>
  <div class ="sidenav e" v-if ="pageNumber===5"><a >{{ uiLabels.sides }}</a></div>
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
      <div class ="nexttosidenav a" v-if ="pageNumber===1">
        <a v-for="item in chosenIngredients"
	    v-if ="item.category===1"
	    >{{item["ingredient_"+lang] }} </a></div>
      <div><a
      v-for="item in chosenIngredients"
      v-if ="item.category===1"
      > {{ item["ingredient_"+lang] }}</a> </div>

    <P class="twentysixpoint"></P>

    <div class ="nexttosidenav b" v-if ="pageNumber===2">
      <a v-for="item in chosenIngredients"
    v-if ="item.category===2">
    {{item["ingredient_"+lang] }} </a></div>
    <div>
      <a v-for="item in chosenIngredients"
    v-if ="item.category===2"
    > {{ item["ingredient_"+lang] }}</a> </div>
<P class="twentysixpoint"></P>

<div class ="nexttosidenav c" v-if ="pageNumber===3"><a
v-for="item in chosenIngredients"
v-if ="item.category===3"
>{{item["ingredient_"+lang] }} </a></div>
<div><a
v-for="item in chosenIngredients"
v-if ="item.category===3"
> {{ item["ingredient_"+lang] }}</a> </div>

<P class="twentysixpoint"></P>

<div class ="nexttosidenav d" v-if ="pageNumber===4"><a
v-for="item in chosenIngredients"
v-if ="item.category===4"
>{{item["ingredient_"+lang] }} </a></div>
<div><a
v-for="item in chosenIngredients"
v-if ="item.category===4"
> {{ item["ingredient_"+lang] }}</a> </div>

    <P class="twentysixpoint"></P>

    <div class ="nexttosidenav e" v-if ="pageNumber===5"><a
    v-for="item in chosenIngredients"
    v-if ="item.category===5"
    >{{item["ingredient_"+lang] }} </a></div>
    <div>
      <a v-for="item in chosenIngredients"
    v-if ="item.category===5">
     {{ item["ingredient_"+lang] }}</a> </div>
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
      <button id="orderButton" v-if="pageNumber===5" v-on:click="placeOrder()">{{ uiLabels.placeOrder }}</button>
      <button id="nextButton" v-if="pageNumber<5" v-on:click="nextPage()">{{ uiLabels.next }}</button>
      <button id="backButton" v-on:click="previousPage()">Back</button>
<!-- <button id= "placeOrder" v-on:click="placeOrder()" > {{ uiLabels.placeOrder }} </button> -->

<form>
<button id="placeOrder" formaction="/#/Payment"> {{ uiLabels.placeOrder }} </button>
</form>


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
      burgerCount: 1,
      bread1:[0,"1"],
      patty1:[0,"1"],
      veggie1:[0,"1"],
      other1:[0,"1"],
      sides1:[0,"1"]

    }
  },
  created: function () {
    this.$store.state.socket.on('orderNumber', function (data) {
      this.orderNumber = data;
    }.bind(this));
  },

methods:
{

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
      else if (this.lang ==="sv") {
        alert("slut på produkt");
      }
      else {
        alert("Out of stock")
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
.firstDiv {
  background-color: blue;
}
/* scoped in the style tag means that these rules will only apply to elements, classes and ids in this template and no other templates. */
#siteTitle {
  display:flex;
  font-weight: bold;

  align-items:center;
  justify-content: center;
  color:black;
  font-style:serif;
  font-size: 20pt;
}

.img {
  position: absolute;
  left: 1.5em;
  top: 11.3em;
  z-index: -1;
}

.img2 {
  position: absolute;
  left: 1.5em;
  top: 16em;
  z-index: -1;

}

.img3 {
  position: absolute;
  left: 1.5em;
  top: 20.6em;
  z-index: -1;
}

.img4 {
  position: absolute;
  left: 1.5em;
  top: 25.2em;
  z-index: -2;
}

.v1 {
  border-left: 2px solid gray;
  height: 700px;

  position: absolute;
  left: 11em;
  top: 5em;
  z-index: -2;
}



.ordering {
  display:grid;
  position: fixed;
}

.upperBorder {
  position:fixed;
  background-color: #fbe2a4;
  width: 100%;
  height: 15%;
  border-color:black;
  border-bottom: 2px solid gray;
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
  background-color: #fbe2a4;
  width: 100%;
  height: 25%;
  border-color:black;
  border-top: 2px solid gray;
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
    z-index: 1;
    top: 8px;
    padding: 2px 1;
}

.nexttosidenav div {
  border: 1px solid black;
  overflow-y:scroll;
  display: flex;
  width: 5em;
  height: 3.5em;
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

}

.sidenav span{
  color:black;


}

.sidenav a:hover span {

  font-weight: bold;
  cursor: pointer;
  color: black;
}


.nexttosidenav{
  position: fixed;
  margin-top: 35%;

    width: 80px;
    z-index: 0;
    top: 8px;

}
.a{
  background-color: #F4A460;
  white-space: pre;

}
.b {
  top:12.27%;
  background-color: #F4A460;
  white-space: pre;


}
.c {
  top:23.3%;
  background-color: #F4A460;
  white-space: pre;


}
.d {
  top:34.5%;
  background-color: #F4A460;
  white-space: pre;


}
.e {
  top:45.5%;

  background-color: #F4A460;
  white-space: pre;

}


.nexttosidenav div {
  border: 1px solid black;
  overflow-y:scroll;
  display: fixed;
  align-items:center;
  width: 5em;
  height: 3.5em;
  white-space: pre;

  border-radius: 25px;

}


.main {
    margin-left: 320px; /* Same width as the sidebar + left position in px */
    font-size: 28px; /* Increased text to enable scrolling */
    padding: 1px 10px;
}
@media screen and (min-height: 900px) and ( min-width: 700px)  {
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

  .a{
    top:0.8%;

  }
  .b {
    top:10.32%;

  }
  .c {
    top:19.89%;
  }
  .d {
    top:29.4%;
  }
  .e {
    top:39%;
  }
  .nexttosidenav div {
    width: 8em;
    height: 5em;
    margin-left: 7em;
  }
  .img {
    left: 2.3em;
    top: 16.7em;
  }
  .img2 {
    left: 2.3em;
    top: 22.9em;
  }
  .img3 {
    left:2.3em;
    top:29em;
  }
  .img4 {
    left:2.3em;
    top: 35.2em;
  }
  .v1 {
    height: 1000px;

    left: 16em;
    top: 5em;
    z-index: -2;
  }
  #siteTitle {
    font-size: 30pt;
  }
}
@media screen and (max-width: 500px) {
  .middlepanel {
  grid-template-columns: auto;

}


}
</style>
