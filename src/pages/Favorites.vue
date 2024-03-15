<script setup>
	import { ref, onMounted } from 'vue';
	import axios from 'axios';
	import CardList from '@/components/CardList.vue';
	import InfoBlock from '@/components/InfoBlock.vue';

	const favorites = ref([]);
	const loading = ref(false);
	const sleep = ms => new Promise(res => setTimeout(res, ms));

	onMounted(async () => {
		try {
			loading.value = true;
			const { data } = await axios.get('https://b35c5b38a32e901a.mokky.dev/favorites?_relations=items');
			favorites.value = data.map(obj => obj.item);
		} catch(err) {
			if (err.response.status === 429) {
				await sleep(100);
			} else {
				console.log(err);
			} 
		} finally {
			loading.value = false;
		}
	});
</script>

<template>
	<h2 class="text-3xl font-bold mb-9">Мои закладки</h2>
	<CardList 
		v-if="favorites.length && !loading"
		:items="favorites" 
		isFavorites 
	/>
	<div v-else-if="loading" class="flex justify-center items-center min-h-96">
		<span class="loader"></span>
	</div>
	<div v-else-if="!favorites.length && !loading" class="flex flex-col h-full justify-center items-center min-h-96">
		<InfoBlock 
			class="mb-9"
			imageUrl="/emoji-1.png"
			title="Закладок нет :("
			description="Вы ничего не добавили в закладки"
		/>
		<router-link to="/">
			<button class="flex gap-3 items-center bg-lime-500 text-white rounded-2xl py-3 px-7 hover:bg-lime-600 active:bg-lime-700 transition">
				<svg
					class="rotate-180"
					width="16"
					height="14"
					viewBox="0 0 16 14"
					fill="none"
					xmlns="http://www.w3.org/2000/svg"
				>
					<path
						d="M1 7H14.7143"
						stroke="white"
						stroke-width="2"
						stroke-linecap="round"
						stroke-linejoin="round" 
					/>
					<path
						d="M8.71436 1L14.7144 7L8.71436 13"
						stroke="white"
						stroke-width="2"
						stroke-linecap="round"
						stroke-linejoin="round" 
					/>
				</svg>
				<span>Вернуться назад</span>
			</button>
		</router-link>
	</div>
</template>

<style>
	.loader {
    width: 48px;
    height: 48px;
    border: 5px solid #a9eea0;
    border-bottom-color: transparent;
    border-radius: 50%;
    display: inline-block;
    box-sizing: border-box;
    animation: rotation 1s linear infinite;
    }

    @keyframes rotation {
    0% {
        transform: rotate(0deg);
    }
    100% {
        transform: rotate(360deg);
    }
    } 
</style>