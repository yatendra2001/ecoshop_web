<template>
    <div v-if="loading"></div>
    <div v-else class="bg-gray-100">
        <transition enter-active-class="animate__animated animate__fadeInLeft"
            leave-active-class="animate__animated animate__fadeOutLeft">
            <Notif v-if="notification.show" :notification="notification" :show="notification.show" />
        </transition>
        <div class="flex flex-col mx-auto space-y-4 lg:w-11/12">
            <div class="hidden -mb-64 lg:flex">
                <img class="w-full"
                    src="https://images-eu.ssl-images-amazon.com/images/G/08/kindle/journeys/YzkyZTQzZGQt/YzkyZTQzZGQt-OTVjNzIxZDIt-w1500._CB659208910_.jpg"
                    alt="" />
            </div>
            <div class="">
                <HomeCategories @showCategorie="showCategorie"></HomeCategories>
            </div>
            <div class="mx-auto">
                <img class="h-56" :src="require('../assets/soldes.jpg')" alt="" />
            </div>
            <div class="flex flex-col bg-white">
                <div class="px-2 py-2 font-light tracking-normal text-gray-700 uppercase bg-yellow-300">
                    <p>Best Sellers</p>
                </div>

                <div class="grid grid-cols-2 gap-2 lg:flex lg:flex-nowrap lg:overflow-x-auto lg:overflow-y-hidden scrollbar scrollbar_delayed"
                    v-if="length">
                    <ProductHome
                        class="flex-none p-4 scrollbar-content lg:transition lg:duration-500 lg:ease-in-out lg:transform lg:hover:scale-105 lg:w-1/4"
                        v-for="(item, index) in width < 700
                                ? availableBestSellers.slice(0, 4)
                                : availableBestSellers" :product="item" :key="index"></ProductHome>
                </div>
            </div>
            <div class="flex flex-col bg-white">
                <div class="px-2 py-2 font-light tracking-normal text-gray-700 uppercase bg-yellow-300">
                    <p>New Products</p>
                </div>

                <div
                    class="grid grid-cols-2 gap-2 lg:flex lg:flex-nowrap lg:overflow-x-auto lg:overflow-y-hidden scrollbar scrollbar_delayed">
                    <ProductHome
                        class="flex-none p-4 scrollbar-content lg:transition lg:duration-500 lg:ease-in-out lg:transform lg:hover:scale-105 lg:w-1/4"
                        v-for="item in width < 700
                                ? newProducts.slice(0, 4)
                                : newProducts" :product="item" :key="item.productId"></ProductHome>
                </div>
            </div>
            <div class="hidden lg:flex">
                <div class="w-1/3">
                    <img class="object-cover h-56 mx-auto w-72" :src="require('../assets/shopping.jpg')" alt="" />
                </div>
                <div class="w-1/3">
                    <img class="object-cover h-56 mx-auto w-72" :src="require('../assets/famille.jpg')" alt="" />
                </div>
                <div class="w-1/3">
                    <img class="object-cover h-56 mx-auto w-72" :src="require('../assets/solde.jpg')" alt="" />
                </div>
            </div>
        </div>
    </div>
</template>
<script>
import HomeCategories from '../components/HomeCategories'

import ProductHome from '../components/ProductHome'
import ProductPromotion from '../components/ProductPromotion'

import { reactive, computed, ref, onMounted } from 'vue'
import { useStore } from 'vuex'
import { useRouter } from 'vue-router'
import store from '@/store'
import useBreakpoints from '../compositionFunctions/useBreakpoints'
import useBestSeller from '../compositionFunctions/bestSeller'
import useNewProducts from '../compositionFunctions/newProducts'
export default {
    components: {
        ProductHome,

        HomeCategories,
        ProductPromotion,
    },
    setup() {
        const store = useStore()
        const router = useRouter()
        const { width } = useBreakpoints()
        const { bestSeller } = useBestSeller()
        const { newProducts } = useNewProducts()
        const promotions = computed(() => {
            return store.getters['promotion/getAlivePromotions']
        })

        const availableBestSellers = computed(() => {
            var tab = []

            data.tab.forEach((params) => {
                if (
                    bestSeller.value.find((product) => {
                        return (
                            product.productId === params.productId &&
                            product.defaultPicture !== ''
                        )
                    })
                ) {
                    tab.push(params)
                }
            })
            return tab
        })
        const length = computed(() => {
            return availableBestSellers.value.length > 0
        })
        const loading = computed(() => {
            return store.state.navigation.loading
        })

        const test = async () => { }
        const data = reactive({
            tab: computed(() => store.state.product.tab),
        })
        const notification = computed(() => {
            if (store.state.notification.notification) {
                return store.state.notification.notification
            } else {
                return { message: '', type: '', show: false }
            }
        })
        const showCategorie = (params) => {
            router.push({ name: 'Categories', query: { id: params } })
        }

        return {
            data,
            test,
            notification,
            loading,
            width,
            bestSeller,
            showCategorie,
            availableBestSellers,
            length,
            promotions,
            newProducts,
        }
    },
}
</script>
<style scoped>
.gradient {
    mask-image: linear-gradient(to bottom, rgba(0, 0, 0, 1), rgba(0, 0, 0, 0));
}
</style>
