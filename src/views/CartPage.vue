<template>
  <ion-page>
    <ion-content>
      <div class="w-full h-[100%] flex flex-col font-lato overflow-hidden slide-up">
        <div class="flex justify-between w-full opacity-50">
          <ion-icon
            @click="$router.go(-1)"
            class="my-5 mx-6 text-3xl"
            name="close-outline"
          ></ion-icon>
          <ion-icon
            class="my-6 mr-8 text-2xl"
            name="chatbox-outline"
          ></ion-icon>
        </div>
        <div class="gap-x-4 flex m-4">
          <span class="text-[0.8rem] opacity-100">SHOPPING BAG</span>
          <span class="text-[0.8rem] opacity-40">FAVOURITES</span>
        </div>

        <div class="overflow-auto h-[80%]">
          <div class="m-4 flex items-center" v-for="(item, index) in cart" :key="index">
            <ion-checkbox
            class="mr-4 mt-28"
             justify="start"
             v-model="item.checked"
         
          > </ion-checkbox
        >
            <IonImg class="w-120 h-full" :src="item.product.productThumbnail" />
            <div class="flex flex-col m-4 w-screen">
              <div class="flex flex-col">
                <span class="font-lato text-[0.9rem]">{{
                  item.product.productName
                }}</span>
                <span class="font-lato text-[0.9rem]"
                  >₱{{ item.product.productPrice }}</span
                >
              </div>
              <div
                @click="removeItem(item.id, item.product.productName)"
                class="mt-auto flex justify-between w-full hover:cursor-pointer"
              >
                <span class="font-lato text-[0.8rem]">DELETE</span>
                <IonIcon name="bookmark-outline" />
              </div>
            </div>
          </div>
        </div>

        <div class="w-full p-4 flex items-center justify-between relative">
          <div @click="$router.push('/tabs/SummaryPage')" class="bg-black py-2 w-3/6 text-center text-white cursor-pointer">
            <span class="font-lato text-[0.9rem]">Checkout</span>
          </div>
          <div class="flex flex-col text-right">
            <span class="text-[0.8rem]">{{ total }} PHP</span>
            <span class="text-[0.8rem]">+ VAT Included</span>
          </div>
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,  
  IonIcon,
  IonImg,
  onIonViewWillEnter,
  IonCheckbox,
} from "@ionic/vue";

import { useCart } from "@/Hooks/useCart";
import { computed, watch } from "vue";

const { cart, fetchCart, removeItem } = useCart();

onIonViewWillEnter(() => {
  fetchCart();
});

const selectedCartItems = computed(() => {
    return cart.value.filter(item => item.checked);
});

const total = computed(() =>
selectedCartItems.value.reduce(
    (sum, item) => sum + item.quantity * item.product.productPrice,
    0
  )
);

watch(
  cart,
  () => {
    const checkedMap: Record<number, boolean> = {};
    cart.value.forEach(item => {
      checkedMap[item.id] = item.checked;
    });
    localStorage.setItem('checkedItem', JSON.stringify(checkedMap)); // Save to localStorage
  },
  { deep: true }
);

</script>

<style scoped>
.slide-up {
  animation: slideUp 0.5s ease-out;
}

@keyframes slideUp {
  from {
    transform: translateY(100%);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}
</style>
