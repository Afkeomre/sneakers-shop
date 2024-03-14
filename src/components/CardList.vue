<script setup>
	import Card from '@/components/Card.vue'

	defineProps({
		items: Array,
		isFavorites: Boolean
	});

	const emit = defineEmits(['addToFavorite', 'onClickPlus']);

</script>

<template>
	<div class="grid grid-cols-4 gap-5" v-auto-animate>
		<Card 
			v-for="item in items"
			:key="item.id"
			:id="item.id"
			:imageUrl="item.imageUrl" 
			:title="item.title" 
			:price="item.price"
			:onClickFavorite="isFavorites ? null : () => emit('addToFavorite', item)"
			:onClickAdd="isFavorites ? null : () => emit('onClickPlus', item)"
			:isFavorite="item.isFavorite"
			:isAdded="item.isAdded"
		/>
	</div>
</template>