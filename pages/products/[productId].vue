<template>
  <main>
    <h1>Product</h1>
    <div v-if="product" class="product">
      <img :src="imageUrl" :alt="product.title" v-if="imageUrl" />
      <div class="product-content">
        <h2>{{ product.title }}</h2>
        <p>{{ product.description }}</p>
        <span>{{ product.price }}</span>
        <button @click="addToCart(product)">Add to cart</button>
      </div>
    </div>
    <div v-else>Loading...</div>
  </main>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import type { IProduct } from '~/types/product.type'
const route = useRoute()
const productId = route.params.productId
const product = ref()
const imageUrl = ref<string | null>('')

onMounted(async () => {
  const response = await useProduct().getProduct(productId as string)
  product.value = response
  console.log('res', response)

  if (response) {
    const productImage = await useStorage().getProductImage(productId as string)
    imageUrl.value = productImage
  }
})

const addToCart = async (product: IProduct) => {
  const item = await useProduct().getProduct(productId as string)
  console.log('item', item)
  useCart().addItem(product)
}
</script>

<style lang="scss" scoped>
main {
  margin-top: 5rem;
}

.product {
  display: flex;
  padding: 1rem;
  gap: 1rem;
  background-color: #f5f5f5;
  border-radius: 5px;

  img {
    width: auto;
    height: 15rem;
    object-fit: cover;
    border-radius: 1rem;
  }

  .product-content {
    display: flex;
    flex-direction: column;
    gap: 1rem;

    h2 {
      font-size: 1.5rem;
    }

    p {
      font-size: 1rem;
    }

    span {
      font-size: 1.5rem;
    }

    button {
      padding: 0.5rem 1rem;
      background-color: #000;
      color: #fff;
      border: none;
      cursor: pointer;

      &:hover {
        background-color: #333;
      }
    }
  }
}
</style>