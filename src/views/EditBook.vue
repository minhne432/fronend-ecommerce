<script setup>
import { ref } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import BookForm from '@/components/BookForm.vue'
import booksService from '@/services/books.service'
import Header from '@/components/Header.vue'
const props = defineProps({
  bookId: { type: String, required: true }
})
const $router = useRouter()
const $route = useRoute()
const book = ref(null)
const message = ref('')

async function getBook() {
  try {
    book.value = JSON.parse(decodeURIComponent($route.query.book))
  } catch (error) {
    console.error('Error parsing book data:', error)
    // Handle the error, e.g., redirect to an error page or display a message
  }
}

async function onUpdateBook(editedBook) {
  try {
    await booksService.updateBook(editedBook.id, editedBook)
    console.log(editedBook)
    message.value = 'Sách được cập nhật thành công.'
  } catch (error) {
    console.log(error)
  }
}
async function onDeleteBook(id) {
  if (confirm('Bạn muốn xóa Sách này?')) {
    try {
      await booksService.deleteBook(id)
      $router.push({ name: 'admin' })
    } catch (error) {
      console.log(error)
    }
  }
}
getBook(props.bookId)
</script>
<template>
  <Header />
  <div v-if="book" class="page">
    <h4>Hiệu chỉnh Sách</h4>
    <BookForm :initial-book="book" @submit:book="onUpdateBook" @delete:book="onDeleteBook" />
    <p>{{ message }}</p>
  </div>
</template>
<style>
.page {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #fff;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  margin-top: 50px;
}

.page h4 {
  text-align: center;
  margin-top: 0;
  padding-bottom: 10px;
  border-bottom: 1px solid #ccc;
}

.page p {
  text-align: center;
  margin-top: 20px;
  color: #888;
}
</style>