<script setup> 
	import { inject, reactive, watch, ref, onMounted } from 'vue';
	import axios from 'axios';
	import debounce  from 'lodash.debounce';
	import CardList from '@/components/CardList.vue';

	const { cart, addToCart, removeFromCart } = inject('cart'); 
	const items = ref([]);

	const filters = reactive({
			sortBy: 'title',
			searchQuery: ''
		});

	const addToFavorite = async (item) => {
		try {
			if (!item.isFavorite) {
				const obj = {
					item_id: item.id,
				};
				item.isFavorite = true;
				const { data } = await axios.post('https://b35c5b38a32e901a.mokky.dev/favorites', obj);
				item.favoriteId = data.id;
			} else {
				item.isFavorite = false;
				await axios.delete(`https://b35c5b38a32e901a.mokky.dev/favorites/${item.favoriteId}`);
				item.favoriteId = null;
			}
		} catch(err) {
			console.log(err);
		}
	};
	
	const onClickPlus = (item) => {
		if (!item.isAdded) {
			addToCart(item);
		} else {
			removeFromCart(item);
		}
	};

	const fetchFavorites = async () => {
		try {
			const { data: favorites } = await axios.get('https://b35c5b38a32e901a.mokky.dev/favorites');

			items.value = items.value.map(item => {
				const favorite = favorites.find(fav => fav.item_id === item.id);

				if (!favorite) {
					return item;
				}

				return {
					...item,
					isFavorite: true,
					favoriteId: favorite.id
				}
			});
		} catch (err) {
			console.log(err);
		}
	};

	const fetchItems = async () => {
		try {
			const params = {
				sortBy: filters.sortBy,
			};

			if (filters.searchQuery) {
				params.title = `*${filters.searchQuery}*`;
			}

			const { data } = await axios.get(
				`https://b35c5b38a32e901a.mokky.dev/items`,
				{ params }
				);

			items.value = data.map(obj => ({
				...obj,
				favoriteId: null,
				isFavorite: false,
				isAdded: false
			}));
		} catch (err) {
			console.log(err);
		}
	};

	onMounted(async () => {
		const localCart = localStorage.getItem('cart');
		cart.value = localCart ? JSON.parse(localCart) : [];

		await fetchItems();
		await fetchFavorites();

		items.value = items.value.map(item => ({
			...item,
			isAdded: cart.value.some(cartItem => cartItem.id === item.id),
		}))
	});

	watch(cart, () => {
		items.value = items.value.map(item => ({
			...item,
			isAdded: false
		}));
	});

	watch(() => filters.sortBy, fetchItems);
	watch(() => filters.searchQuery, debounce(fetchItems, 300));

</script>

<template>
	<div class="flex justify-between items-center mb-9">
		<h2 class="text-3xl font-bold">Все кроссовки</h2>

		<div class="flex gap-8">
			<select v-model="filters.sortBy" name="" id="" class="py-2 px-3 border rounded-lg outline-none">
				<option disabled value="">Выберите способ сортировки</option>
				<option value="title">По названию</option>
				<option value="price">По цене (сначала дешевые)</option>
				<option value="-price">По цене (сначала дорогие)</option>
			</select>
		<div class="relative">
			<img src="/search.svg" alt="Search" class="absolute top-3 left-4">
			<input 
				v-model="filters.searchQuery" 
				type="text" 
				placeholder="Поиск..." 
				class="border rounded-lg outline-none py-2 pl-12 pr-4 focus:border-gray-400"
			>
		</div>
		</div>
	</div>

	<CardList :items="items" @addToFavorite="addToFavorite" @onClickPlus="onClickPlus" />
</template>