<template>
  <div>
    <ul v-if="cart.length > 0">
      <li v-for="(item, index) in cart" :key="index">
        {{ item }} <span class="remove" @click="removeItem(item)">X</span>
      </li>
    </ul>
    <p v-else>Your cart is empty, please add an product</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cart: []
    }
  },
  methods: {
    removeItem(item) {
      // event emiter to remove item directly from cart
      this.$root.$emit('removeItemFromCart', item)
    },
    // get itens from cart
    async getCart() {
      await this.$localForage.getItem('cart').then((itens) => {
        this.cart = itens
      })
    }
  },
  mounted() {
    this.getCart()
    // event listener from card itens to update cart
    this.$root.$on('updateCart', itens => {
      this.cart = itens
    })
  }
}
</script>
