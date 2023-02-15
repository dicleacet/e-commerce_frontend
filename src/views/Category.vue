<template>
    <div class="page-category">
        <div class="columns is-multiline">
            <div class="column is-12">
                <h2 class="is-size-2 has-text-centered">{{ category.name }}</h2>
            </div>

            <ProductBox v-for="product in category.products" v-bind:key="product.id" v-bind:product="product" />

        </div>
    </div>
</template>


<script>
import axios from 'axios'
import { toast } from 'bulma-toast';

import ProductBox from '@/components/ProductBox.vue'

export default {
    name: 'Category',
    data() {
        return {
            category: {
                products: []
            }
        }
    },
    components: {
        ProductBox
    },
    mounted() {
        this.getCategory()
    },
    watch: {
        '$route.params.category_slug': function () {
            this.getCategory()
        }
    },
    methods: {
        async getCategory() {
            const categorySlug = this.$route.params.category_slug
            this.$store.commit('setIsLoading', true)

            await axios.get(`api/v1/products/${categorySlug}/`)
                .then(response => {
                    this.category = response.data

                    document.title = `${this.category.name} | Django Ecommerce`
                })
                .catch(error => {
                    console.log(error)

                    toast({
                        message: 'Something went wrong',
                        type: 'is-danger',
                        position: 'top-center',
                        dismissible: true,
                        pauseOnHover: true,
                        duration: 2000,
                        animate: { in: 'fadeIn', out: 'fadeOut' }
                    })
                })
                .finally(() => {
                    this.$store.commit('setIsLoading', false)
                })
        }
    }
}
</script>