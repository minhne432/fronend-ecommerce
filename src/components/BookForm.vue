<script setup>
import { ref } from "vue";

const props = defineProps({
  initialBook: { type: Object, required: true },
});
const $emit = defineEmits(["submit:book", "delete:book"]);

const editedBook = ref({ ...props.initialBook });
const selectedImage = ref(null);

function handleImageUpload(event) {
  const file = event.target.files[0];
  selectedImage.value = file;
  console.log(selectedImage.value);

  // Cập nhật trường 'image' trong editedBook để truyền thông tin ảnh cho EditBook.vue
  editedBook.value.image = file;
  console.log(editedBook.value.image)
}

function deleteBook() {
  $emit("delete:book", editedBook.value.id);
}
function submitBook() {
  $emit("submit:book", editedBook.value);
}



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
          <label for="name">Tên sách</label>
          <input id="name" type="text" class="form-control" v-model="editedBook.name" />

          <label for="author">Thể loại</label>
          <input id="author" type="text" class="form-control" v-model="editedBook.categoryName" />

            <label for="author">Mô tả</label>
            <input id="description" type="text" class="form-control" v-model="editedBook.description" />

          <label for="price">Giá</label>
          <input id="price" type="text" class="form-control" v-model="editedBook.price" />

          <!-- Nút "Chọn ảnh" -->
          <input type="file" id="image" ref="imageInput" style="display: none" @change="handleImageUpload" />
          <label for="image" class="image-upload-button additional-image-upload">Chọn ảnh khác</label>
        </div>
      </div>

    <div class="form-group">
      <button>Lưu</button>
      <button class="delete-button"
        v-if="editedBook.id"
        type="button"
        @click="deleteBook"
      >
        Xóa
      </button>
    </div>
  </form>
</template>

<style scoped>
/* CSS cho form và các phần tử bên trong */
.form-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

input[type="text"],
input[type="file"],
button {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box; /* Đảm bảo padding không tăng kích thước của phần tử */
  font-size: 16px;
  margin-top: 5px;
}

button {
  background-color: #007bff;
  color: #fff;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}

/* Tùy chỉnh cho nút Xóa */
button.delete-button {
  background-color: #dc3545;
}

button.delete-button:hover {
  background-color: #bd2130;
}

/* Ẩn input type file và tùy chỉnh nút chọn ảnh */
input[type="file"] {
  display: none;
}

.image-upload-button {
  display: inline-block;
  padding: 10px 15px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f0f0f0;
  cursor: pointer;
  margin-top: 5px;
}

/* Hiển thị các hình ảnh được chọn */
.image-preview {
  max-width: 100%;
  height: auto;
  margin-top: 10px;
}

/* Tuỳ chỉnh kích thước ảnh xem trước */
.image-preview img {
  max-width: 100%;
  height: auto;
  border-radius: 5px;
}

/* CSS cho nút "Chọn ảnh khác" */
.image-upload-button {
  display: inline-block;
  padding: 10px 15px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f0f0f0;
  cursor: pointer;
  margin-top: 5px;
  color: #333;
}

.image-upload-button:hover {
  background-color: #e0e0e0;
}
.form-group {
  display: flex;
  align-items: center;
}

.image-preview {
  max-width: 150px; /* Điều chỉnh kích thước của hình ảnh */
  margin-right: 20px; /* Khoảng cách giữa hình ảnh và ô input */
}

.input-fields {
  flex: 1; /* Ô input sẽ chiếm phần còn lại của không gian trống */
}

.delete-button {
  background-color: #dc3545;
  color: #fff;
}

.delete-button:hover {
  background-color: #bd2130;
}
</style>
