<template>
  <div id="app">
    <Product 
    v-for="product in products"
    :key="product.code"
    v-bind="product"
    />
  </div>
</template>

<script>
import products from './data/products'
export default {
  name: 'App',
  components: {
    'Product': () => import('./components/Product'),
  },
  data() {
      return {
          products: products
      }
  },
}
</script>

<style>
.product {
  margin-bottom: 20px;
}
</style>
