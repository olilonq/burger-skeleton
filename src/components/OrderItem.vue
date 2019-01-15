<template>
<div class="">


	<div style ="text-align:center">
		{{orderId}} {{order.type}}: {{ order.ingredients.map(item=>item["ingredient_"+ lang]).join(", ") }}

	</div>
	<button style="height: 50px; text-align:center; padding: 0px; width: 70px; border-radius: 50%; float: right;" v-if= "order.status === 'done'" v-on:click="orderServed">
		{{uiLabels.served}}
	</button>

	<div v-if= "order.status === 'not-started'">


		<h3 style="font-family:'Bree Serif';text-align: center;"> {{uiLabels.clickToPay}}  </h3>

	<div class ="orderDiv"  v-on:click="orderPaid">
		<form id= "paymentButton">

			<input type="image" src="https://ecommercenews.eu/wp-content/uploads/2013/06/most_common_payment_methods_in_europe-740x393.png"  width="200" height="120" formaction="/#/"  >

		</form>

</div>

<h4 style="text-align: center">
	{{uiLabels.orderNumber}} {{orderId}}
</h4>
</div>

</div>
</template>
<script>
export default {
  name: 'OrderItem',
  props: {
    uiLabels: Object,
    order: Object,
    orderId: String,
    lang: String
  },

	methods: {

	orderServed: function() {
		this.$emit('served');
	},

	orderPaid: function() {
		this.$emit('paid');
	}

}
}
</script>
<style scoped>

.orderDiv {
		display: block;
    margin-left: 3.3em;
    margin-right: 30%
}
@media screen and (min-height: 900px) and (max-height: 1100px) and ( min-width: 700px) and (max-width: 850px) {
		.orderDiv{
			margin-left: 13em;
			}
}
</style>
