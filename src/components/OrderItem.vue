<template>
<div class="">


	<div>
		{{orderId}} {{order.type}} : {{ order.ingredients.map(item=>item["ingredient_"+ lang]).join(", ") }}

	</div>
	<button v-if= "order.status === 'done'" v-on:click="orderServed">
		Served
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
