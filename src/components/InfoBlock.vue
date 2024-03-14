<script setup>
	defineProps({
		imageUrl: String,
		title: String,
		description: String
	})
</script>


<template>
	<div class="flex flex-col items-center max-w-72 mx-auto">
		<img class="w-20 h-20 mb-4" :src="imageUrl" alt="Info image">
		<h2 class="text-2xl font-medium mb-2">{{ title }}</h2>
		<p class="text-gray-400 text-center">{{ description }}</p>
	</div>
</template>