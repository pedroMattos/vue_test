<template>
  <SfProductCard
  :image="_product.img"
  :image-height="150"
  :image-width="215"
  :regular-price="`$ ${_product.price}`"
  :special-price="_product.specialPrice ? `$ ${_product.specialPrice}` : null"
  :title="_product.title"
  :show-add-to-cart-button="true"
  @click:add-to-cart="teste(_product)"
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
    teste(item) {
      this._product.addOnCart = !this._product.addOnCart
      this.addOrRemoveItens({ itemToAddOrRemove: item })
    },
    async saveCart (itemAdded) {
      // checar quantidade
      await this.$localForage.setItem('cart', itemAdded).then(() => {
        console.log('success')
      }).catch((err) => {
        throw new Error(err)
      })
    },
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
    async removeItemFromCart({ itemToremove }) {
      let itensFromDB = await this.$localForage.getItem('cart').then((itens) => itens)
      console.log(itemToremove, 'item para remover', itensFromDB, 'itens do banco')
      this.saveCart(itensFromDB.filter((el) => {
        if (itemToremove._id !== el._id) return el
      }))
    },
    async checkIsOnCart() {
      let itensFromDB = await this.$localForage.getItem('cart').then((itens) => itens)
      this._product.addOnCart = itensFromDB.find((el) => el._id === this._product._id) !== undefined
      ? true : false
    }
  },
  mounted() {
    this.checkIsOnCart()
  },
}
</script>
