<script setup>
import { ref, onMounted } from 'vue'

const props = defineProps({
  initialBook: { type: Object, required: true }
})
const $emit = defineEmits(['submit:book', 'delete:book'])

const editedBook = ref({ ...props.initialBook })
const selectedImage = ref(null)

function handleImageUpload(event) {
  const file = event.target.files[0]
  selectedImage.value = file
  console.log(selectedImage.value)

  // Cập nhật trường 'image' trong editedBook để truyền thông tin ảnh cho EditBook.vue
  editedBook.value.image = file
  console.log(editedBook.value)

  const reader = new FileReader()

  reader.onload = () => {
    editedBook.value.thumbnail = reader.result // Lưu đường dẫn ảnh vào editedBook
  }

  if (file) {
    reader.readAsDataURL(file)
  }
}

function deleteBook() {
  $emit('delete:book', editedBook.value.id)
}
function submitBook() {
  $emit('submit:book', editedBook.value)
}

const categories = ref([])

// Hàm gọi API để lấy danh sách các danh mục
async function fetchCategories() {
  try {
    const response = await fetch('http://localhost:3000/api/products/categories/getall')
    if (!response.ok) {
      throw new Error('Không thể lấy dữ liệu từ server')
    }
    const data = await response.json()
    categories.value = data // Lưu danh sách danh mục vào biến categories
  } catch (error) {
    console.error('Lỗi khi lấy danh sách danh mục:', error)
  }
}

// Gọi hàm fetchCategories khi component được mounted
onMounted(() => {
  fetchCategories()
})
</script>




<template>
  <form @submit.prevent="submitBook">
    <div class="form-group">
      <!-- Hiển thị ảnh -->
      <div class="image-preview" v-if="editedBook.thumbnail">
        <img :src="editedBook.thumbnail" alt="Product Image" />
      </div>
      <!-- Các ô input -->
      <div class="input-fields">
        <label for="name">Tên sản phẩm</label>
        <input id="name" type="text" class="form-control" v-model="editedBook.name" />

        <label for="author">Thương hiệu</label>

        <select v-model="editedBook.category_id">
          <option v-for="category in categories" :key="category.id" :value="category.id">
            {{ category.name }}
          </option>
        </select>
        <label for="author">Mô tả</label>
        <input id="description" type="text" class="form-control" v-model="editedBook.description" />

        <label for="price">Giá</label>
        <input id="price" type="text" class="form-control" v-model="editedBook.price" />

        <!-- Nút "Chọn ảnh" -->
        <input
          type="file"
          id="image"
          ref="imageInput"
          style="display: none"
          @change="handleImageUpload"
        />
        <label for="image" class="image-upload-button additional-image-upload">Chọn ảnh khác</label>
      </div>
    </div>

    <div class="form-group">
      <button>Lưu</button>
      <button class="delete-button" v-if="editedBook.id" type="button" @click="deleteBook">
        Xóa
      </button>
    </div>
  </form>
</template>

<style scoped>
.book-form {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f5f5f5;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.form-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

input[type='text'],
textarea,
button,
input[type='file'] {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box;
  font-size: 16px;
  margin-top: 5px;
}

textarea {
  resize: vertical;
}

button {
  background-color: #333;
  color: #fff;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #474c51;
}

.delete-button {
  background-color: #dc3545;
}

.delete-button:hover {
  background-color: #bd2130;
}

.image-upload-button {
  display: inline-block;
  padding: 10px 15px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f0f0f0;
  cursor: pointer;
  margin-top: 5px;
  color: #333;
  transition: background-color 0.3s ease;
}

.image-upload-button:hover {
  background-color: #e0e0e0;
}

.image-preview {
  max-width: 100%;
  height: auto;
  margin-top: 10px;
}

.image-preview img {
  max-width: 100%;
  height: auto;
  border-radius: 5px;
}

.form-group {
  display: flex;
  align-items: center;
}

.image-preview {
  max-width: 150px;
  margin-right: 20px;
}

.input-fields {
  flex: 1;
}

.save-button {
  background-color: #28a745;
}

.save-button:hover {
  background-color: #218838;
}

select {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 16px;
  margin-top: 5px;
  appearance: none; /* Loại bỏ giao diện mặc định của select box */
  background-color: white; /* Màu nền của select box */
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='12' viewBox='0 0 24 24' fill='none' stroke='%23333' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3E%3Cpolyline points='6 9 12 15 18 9'%3E%3C/polyline%3E%3C/svg%3E"); /* Icon mũi tên */
  background-repeat: no-repeat;
  background-position-x: 100%;
  background-position-y: center;
  cursor: pointer;
}

/* Hover effect */
select:hover {
  border-color: #aaa;
}

/* Focus effect */
select:focus {
  outline: none;
  border-color: #007bff;
  box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
}
</style>