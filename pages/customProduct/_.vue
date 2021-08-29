<template>
  <div>
   {{ product }} 
  </div>
</template>

<script>
import {SfProductCard} from '@storefront-ui/vue'
import axios from 'axios'

export default {
  components: {
    SfProductCard,
  },
  data() {
    return {
      product: null
    }
  },
  methods: {
    getProducts () {
      axios.get('http://localhost:3333/products').then((products) => {
        this.product = products.data.filter((el) => {
          if (el._id == this.$route.params.pathMatch) return el
        })
      })
    },
  },
  beforeMount() {
    this.getProducts()
  }
}
</script>
