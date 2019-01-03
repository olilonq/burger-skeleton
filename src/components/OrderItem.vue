<template>
<div class="">


	<div>
		map(item=>item["ingredient_"+ lang]).join(", ") }}
				{{orderId}} {{order.type}} {{uiLabels.ingredients}}:
		    {{ order.ingredients.map(item=>item.burgerCount + " " + item["ingredient_"+ lang]).join(", ") }}



	</div>
	<button style="height: 50px; text-align:center; padding: 0px; width: 70px; border-radius: 50%; float: right;" v-if= "order.status === 'done'" v-on:click="orderServed">
		{{uiLabels.served}}
	</button>
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
	}

}
}
</script>
<style scoped>

</style>
