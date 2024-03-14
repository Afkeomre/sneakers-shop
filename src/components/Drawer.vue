<script setup>
	import axios from 'axios';
	import { ref, computed, inject } from 'vue';
	import DrawerHead from '@/components/DrawerHead.vue';
	import CartItemList from '@/components/CartItemList.vue';
	import InfoBlock from '@/components/InfoBlock.vue';

	const props = defineProps({
		totalPrice: Number,
		vat: Number,
	});

	const {	cart } = inject('cart');

	const isCreating = ref(false);

	const orderId = ref(null);

	const createOrder = async () => {
		try {
			isCreating.value = true;
			const { data } = await axios.post('https://b35c5b38a32e901a.mokky.dev/orders', {
				items: cart.value,
				totalPrice: props.totalPrice,
			});

			cart.value = [];

			orderId.value = data.id;
		} catch (err) {
			console.log(err);
		} finally {
			isCreating.value = false;
		}
	};

	const emit = defineEmits(['closeDrawer']);
	const cartIsEmpty = computed(() => cart.value.length === 0);
	const buttonDisabled = computed(() => isCreating.value || cartIsEmpty.value);
</script>

<template>
	<div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70 transition" @click="emit('closeDrawer')"></div>
	<div class="fixed top-0 right-0 w-96 h-full bg-white z-20 p-8 overflow-y-auto">

		<div v-if="totalPrice" class="flex flex-col min-h-full justify-between">
			<div>
				<DrawerHead @closeDrawer="emit('closeDrawer')" />
				<CartItemList />
			</div>

			<div class="flex flex-col gap-3">
				<div class="flex flex-col gap-3 mb-4">
					<div class="flex gap-2">
						<span>Итого:</span>
						<div class="flex-1 border-b border-dashed"></div>
						<b>{{ totalPrice }} руб.</b>
					</div>

					<div class="flex gap-2">
						<span>Налог 5%:</span>
						<div class="flex-1 border-b border-dashed"></div>
						<b>{{ vat }} руб.</b>
					</div>
				</div>

				<button 
					:disabled="buttonDisabled"
					class="bg-lime-500 text-white w-full rounded-2xl py-3 disabled:bg-slate-300 hover:bg-lime-600 active:bg-lime-700 transition cursor-pointer"
					@click="createOrder"
				>
					Оформить заказ
				</button>
			</div>
		</div>

		<div class="h-full" v-else>
			<DrawerHead @closeDrawer="emit('closeDrawer')" />

			<div class="flex h-5/6 items-center">
				<InfoBlock 
					v-if="!orderId"
					imageUrl="/package-icon.png" 
					title="Корзина пуста" 
					description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ."
				/>
				<InfoBlock 
					v-else
					imageUrl="/order-success-icon.png" 
					title="Заказ оформлен!" 
					:description="`Ваш заказ №${orderId} скоро будет передан курьерской доставке`"
				/>
			</div>
		</div>
	</div>
</template>