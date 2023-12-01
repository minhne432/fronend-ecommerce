<script setup>
import { ref } from 'vue'
import { useRoute } from 'vue-router'
import Header from '@/components/Header.vue'
import Footer from '@/components/Footer.vue'

import BookCard from '@/components/BookCard.vue'
import orderService from '../services/order.service'
const props = defineProps({
  bookId: { type: String, required: true }
})

const $route = useRoute()
const book = ref(null)
const message = ref('')

async function getBook() {
  try {
    book.value = JSON.parse(decodeURIComponent($route.query.book))
    console.log(book.value)
  } catch (error) {
    console.error('Error parsing book data:', error)
    // Handle the error, e.g., redirect to an error page or display a message
  }
}

async function addToCart(details) {
  const cartDetails = {
    product_id: details.bookDetails.id,
    quantity: details.quantity,
    user_id: localStorage.getItem('id')
  }
  try {
    await orderService.addToCart(cartDetails)
    message.value = 'Thêm sách vào giỏ hàng thành công!!'
  } catch (error) {
    console.log(error)
  }
}

getBook(props.bookId)
</script>
<template>
  <Header />
  <div v-if="book" class="page">
    <div v-if="message" class="success-message">
      {{ message }}
    </div>
    <BookCard :initial-book="book" @submit:order="addToCart" />
  </div>

  <Footer />
</template>
<style>
/* CSS cho thông báo thành công */
.success-message {
  background-color: #4caf50;
  color: white;
  padding: 10px;
  margin-top: 20px;
  border-radius: 5px;
  text-align: center;
}
</style>