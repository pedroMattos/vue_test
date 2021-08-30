<template>
  <SfProductCard
  :image="_product.img"
  :image-height="150"
  :image-width="215"
  :regular-price="`$ ${_product.price}`"
  :special-price="_product.specialPrice ? `$ ${_product.specialPrice}` : null"
  :title="_product.title"
  :show-add-to-cart-button="true"
  @click:add-to-cart="cart(_product)"
  :is-added-to-cart="_product.addOnCart"
  :wishlist-icon="null"
  />
</template>

<script>
import { SfProductCard } from '@storefront-ui/vue'

export default {
  components: {
    SfProductCard
  },
  props: {
    value: { type: Object, default: () => ({}) }
  },
  computed: {
    _product() {
      return this.value
    }
  },
  methods: {
    /**
     * cart control
     */
    cart(item) {
      this._product.addOnCart = !this._product.addOnCart
      this.addOrRemoveItens({ itemToAddOrRemove: item })
    },
    /**
     * this function will save itens on cart
     */
    async saveCart (itemAdded) {
      // checar quantidade
      await this.$localForage.setItem('cart', itemAdded).then(() => {
        console.log('success')
        this.$root.$emit('updateCart', itemAdded)
      }).catch((err) => {
        throw new Error(err)
      })
    },
    /**
     * @param {Object} itemToAddOrRemove - Obrigatory param to get addOnCArt value
     * this funciont will switch according to current value 'addOnCart'
     */
    addOrRemoveItens ({ itemToAddOrRemove }) {
      switch (itemToAddOrRemove.addOnCart) {
        case false:
          console.log('remover')
          this.removeItemFromCart({ itemToremove: itemToAddOrRemove })
          break;
        default:
          console.log('adicionar')
          this.addItemOnCart({ itemToAdd: itemToAddOrRemove })
          break;
      }
    },
    /**
     * @param {Object} itemToAdd - Obrigatory param to make 'push' on products array
     * this function will receive item to add on cart
     */
    async addItemOnCart({ itemToAdd }) {
      let itensFromDB = await this.$localForage.getItem('cart').then((itens) => itens)
      if (Array.isArray(itensFromDB))
      this.saveCart([...itensFromDB,
      itemToAdd].filter((el) => {
        return el != null;
      }))
      else if (typeof itensFromDB === 'object')
      this.saveCart([itensFromDB,
      itemToAdd].filter((el) => {
        return el != null;
      }))
    },
    /**
     * this function will receive item to remove from cart
     * @param {Object} itemToremove - Obrigatory param to make a query
     */
    async removeItemFromCart({ itemToremove }) {
      let itensFromDB = await this.$localForage.getItem('cart').then((itens) => itens)
      this.saveCart(itensFromDB.filter((el) => {
        if (itemToremove._id !== el._id) return el
      }))
    },
    /**
     * this function will check if exist any item on cart, to set up active status on
     * cart icon
     */
    async checkIsOnCart() {
      let itensFromDB = await this.$localForage.getItem('cart').then((itens) => itens)
      this._product.addOnCart = itensFromDB.find((el) => el._id === this._product._id) !== undefined
      ? true : false
    }
  },
  mounted() {
    this.checkIsOnCart()
    // event listener to remove item directly from cart
    this.$root.$on('removeItemFromCart', item => {
      this.removeItemFromCart({ itemToremove: item })
      this.checkIsOnCart()
    })
  },
}
</script>
