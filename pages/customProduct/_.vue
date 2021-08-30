<template>
  <section>
    <nav-bar />
    <div v-if="update" class="banner">
      <img :src="product[0].img" :alt="product[0].title">

      <div class="infos">
        <h1>{{ product[0].title }}</h1>
      </div>
    </div>
  </section>
</template>

<script>
import NavBar from '../../components/NavBar'
import axios from 'axios'

export default {
  components: {
    NavBar
  },
  data() {
    return {
      product: null,
      update: false
    }
  },
  methods: {
    getProducts () {
      axios.get('http://localhost:3333/products').then((products) => {
        this.product = products.data.filter((el) => {
          if (el._id == this.$route.params.pathMatch) {
            document.title = `My store - ${el.title}`
            this.update = true
            return el
          }
        })
      })
    },
  },
  beforeMount() {
    this.getProducts()
  }
}
</script>
