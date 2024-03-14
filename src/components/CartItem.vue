<script setup>
	defineProps({
		id: Number,
		title: String,
		imageUrl: String,
		price: Number
	});

	const emit = defineEmits(['onClickRemove']);

</script>

<template>
	<div class="flex items-center border border-slate-200 rounded-2xl p-4 gap-4">
		<img :src="imageUrl" alt="Sneaker" class="w-16 h-16">
		<div class="flex flex-col w-full">
			<p class="mb-2 leading-tight max-w-40">{{ title }}</p>

			<div class="flex justify-between">
				<b>{{ price }}</b>
				<img
					src="/close.svg"
					alt="Close"
					class="opacity-40 hover:opacity-100 cursor-pointer transition"
					@click="emit('onClickRemove')"
				>
			</div>
		</div>
	</div>
</template>