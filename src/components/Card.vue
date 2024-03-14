<script setup>
	defineProps({
		id: Number,
		imageUrl: String,
		title: String,
		price: Number,
		isFavorite: Boolean,
		isAdded: Boolean,
		onClickFavorite: Function,
		onClickAdd: Function
	});

</script>

<template>
	<div class="relative bg-white border border-slate-100 rounded-3xl p-8 cursor-pointer transition hover:-translate-y-2 hover:shadow-xl">
		<img 
			v-if="onClickFavorite"
			:src="isFavorite ? '/like-2.svg' : '/like-1.svg'" 
			alt="Like" 
			class="asolute top-8 right-8"
			@click="onClickFavorite"
		>
		<img :src="imageUrl" alt="Sneaker" class="mb-2">
		<p class="mb-3">{{ title }}</p>

		<div class="flex justify-between">
			<div class="flex flex-col">
				<span class="text-slate-400">Цена:</span>
				<b>{{ price }} руб.</b>
			</div>
			<img 
				v-if="onClickAdd"
				:src="isAdded ? '/checked.svg' : '/plus.svg'" 
				alt="Plus" 
				@click="onClickAdd">
		</div>
	</div>
</template>