<template>
  <Header />
  <div class="search-bar">
    <input v-model="searchText" />
    <button id="searchButton">Tìm</button>
  </div>

  <main>
    <section
      class="product"
      v-for="book in filteredProducts"
      :key="book.id"
      @click="() => goToDetailsBook(book)"
    >
      <img :src="book.thumbnail" alt="ảnh bìa" />
      <h2>{{ book.name }}</h2>
      <p><strong>Giá:</strong> {{ book.price.toLocaleString('vi-VN') }}</p>
      <button>Mua hàng</button>
    </section>

    <!-- Có thể thêm nhiều sản phẩm khác ở đây bằng cách sao chép đoạn mã section.product và chỉnh sửa thông tin -->
  </main>

  <Pagination
    :totalPages="totalPages"
    :currentPage="currentPage"
    :length="length"
    @update:currentPage="handlePageChange"
  />
  <footer>
    <p>&copy; 2023 Website Bán Nước Hoa</p>
  </footer>
</template>

<script setup>
import { ref, computed, onMounted, watchEffect } from 'vue'
import { useRouter } from 'vue-router'
// attach sidebar into shopIndex
import Header from '@/components/Header.vue'
import Pagination from '@/components/Pagination.vue'

import booksService from '@/services/books.service'

// Define the variables with `ref` if they're reactive
const currentPage = ref(1)
const totalPages = ref(1)
const length = 5

const searchText = ref('')
const books = ref([])

// // Comnputed

const searchableProducts = computed(() =>
  books.value.map((book) => {
    const { id, name, price, thumbnail, categoryName } = book
    return [id, name, price, thumbnail, categoryName].join('')
  })
)

const filteredProducts = computed(() => {
  if (!searchText.value) {
    // const data = books.value;
    const data = books.value
    console.log(data)
    return data
  }
  console.log(books.value)
  return books.value.filter((books, index) =>
    searchableProducts.value[index].includes(searchText.value)
  )
})

// Method
const handlePageChange = (newPage) => {
  currentPage.value = newPage
}

const $router = useRouter()

const goToDetailsBook = (book) => {
  $router.push({
    name: 'book.details',
    params: { id: book.id },
    query: { book: JSON.stringify(book) }
  })
}

async function retrieveBooks(page) {
  try {
    const chunk = await booksService.getBooks(page) // Gọi API để lấy danh sách sách
    //console.log(chunk)
    totalPages.value = chunk.metadata.lastPage ?? 1 // Cập nhật tổng số trang
    //console.log(chunk.metadata.lastPage)
    books.value = chunk.products // Lưu danh sách sách từ API vào biến books
    console.log(books.value)
  } catch (error) {
    console.log(error) // Xử lý lỗi nếu có
  }
}

// When this component is mounted, load the first page of contacts
onMounted(() => retrieveBooks(1))

// When currentPage changes, fetch contacts for currentPage
watchEffect(() => retrieveBooks(currentPage.value))
</script>

<style scoped>
/* Reset CSS */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
  line-height: 1.6;
  background-color: #f4f4f4;
}

header {
  background-color: #333;
  color: #fff;
  padding: 20px;
  text-align: center;
}

header nav ul {
  list-style: none;
}

header nav ul li {
  display: inline;
  margin-right: 20px;
}

header nav ul li a {
  color: #fff;
  text-decoration: none;
}

main {
  padding: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
}

.product {
  margin: 20px;
  padding: 20px;
  background-color: #fff;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  text-align: center;
  width: 300px;
}

.product img {
  width: 200px;
  height: 300px;
  object-fit: cover;
  border-radius: 5px;
  margin-bottom: 10px;
}

.product h2 {
  font-size: 1.5em;
  margin-bottom: 10px;
}

button {
  padding: 10px 20px;
  background-color: #333;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #555;
}

footer {
  width: 100%;
  background-color: #333;
  color: #fff;
  text-align: center;
  padding: 20px 0;
}

/* CSS cho thanh tìm kiếm */
.search-bar {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

#searchInput {
  padding: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
  margin-right: 5px;
}

#searchButton {
  padding: 10px 20px;
  background-color: #333;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

#searchButton:hover {
  background-color: #555;
}
</style>