<template>
  <section>
    <nav-bar />
    <b-container>
      <b-row>
        <b-col cols="3" v-for="(product, i) in products" :key="i">
          <nuxt-link :to="`/customProduct/${product._id}`">
            <product-component v-model="products[i]" />
          </nuxt-link>
        </b-col>
      </b-row>
      <b-row>
        <my-cart />
      </b-row>
    </b-container>
  </section>
</template>

<script>
import {SfProductCard} from '@storefront-ui/vue'
import ProductComponent from '../components/ProductComponent'
import NavBar from '../components/NavBar'
import MyCart from '../components/myCart'
import axios from 'axios'

export default {
  components: {
    SfProductCard,
    ProductComponent,
    MyCart,
    NavBar
  },
  data() {
    return {
      products: null
    }
  },
  methods: {
    getProducts () {
      axios.get('http://localhost:3333/products').then((products) => {
        this.products = [...products.data]
      })
    },
  },
  beforeMount() {
    this.getProducts()
    document.title = 'My Store'
  }
}
</script>
