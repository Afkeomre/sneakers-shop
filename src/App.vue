<script setup>
	import { ref, watch, provide, computed } from 'vue';
	import Header from '@/components/Header.vue';
	import Drawer from '@/components/Drawer.vue';

	const cart = ref([]);

	const drawerOpen = ref(false);

	const openDrawer = () => {
		drawerOpen.value = true;
	};

	const closeDrawer = () => {
		drawerOpen.value = false;
	};

	const addToCart = (item) => {
		cart.value.push(item);
		item.isAdded = true;
	};

	const removeFromCart = (item) => {
		cart.value.splice(cart.value.indexOf(item), 1);
		item.isAdded = false;
	};

	const cartSum = computed(() => {
		return cart.value.reduce((acc, item) => acc + item.price, 0);
	});

	const vatSum = computed(() => {
		return Math.round(cartSum.value * 5 / 100);
	});

	watch(cart, () => {
		localStorage.setItem('cart', JSON.stringify(cart.value));
	},
	{
		deep: true
	});

	watch(drawerOpen, () => {
		document.body.style.overflow = drawerOpen.value ? 'hidden' : 'auto';
	});

	provide('cart', {
		cart,
		addToCart,
		removeFromCart,
	});

</script>

<template>
	<Drawer 
		v-if="drawerOpen" 
		:totalPrice="cartSum" 
		:vat="vatSum" 
		@closeDrawer="closeDrawer" 
	/>

	<div class="bg-white w-4/5 mx-auto rounded-xl shadow-xl my-14">
		<Header :totalPrice="cartSum" @openDrawer="openDrawer" />

		<div class="p-10">
			<router-view></router-view>
		</div>

	</div>
</template>

