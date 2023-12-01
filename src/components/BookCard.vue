<template>
  <div class="product">
    <img :src="bookdetails.thumbnail" alt="Book Title" class="product-image" />
    <div class="product-details">
      <h2 class="title">Tên Sách: {{ bookdetails.name }}</h2>
      <p class="author">Thể loại: {{ bookdetails.categoryName }}</p>
      <p class="description">Mô tả: {{ bookdetails.description }}</p>
      <p class="price">Giá: {{ bookdetails.price }} đ</p>
      <div class="quantity-section">
        <label for="quantity">Số lượng:</label>
        <input
          type="number"
          id="quantity"
          name="quantity"
          min="1"
          v-model="quantity"
          class="quantity-input"
        />
      </div>
      <form @submit.prevent="submitOrder">
        <div class="check_quantity" v-if="isQuantityInvalid()">
          Số lượng sản phẩm không được nhỏ hơn 1
        </div>
        <button
          type="submit"
          class="add-to-cart-btn"
          v-if="!isQuantityInvalid()"
          :disabled="isQuantityInvalid()"
        >
          Thêm vào giỏ hàng
        </button>
      </form>
    </div>
  </div>
</template>

<script setup>
import { defineProps, ref } from 'vue'
const $emit = defineEmits(['submit:order'])
const props = defineProps({
  initialBook: { type: Object, required: true }
})

const bookdetails = ref({ ...props.initialBook })
const quantity = ref(1)

function submitOrder() {
  const orderDetails = {
    bookDetails: bookdetails.value,
    quantity: quantity.value
  }
  $emit('submit:order', orderDetails)
}

function isQuantityInvalid() {
  return quantity.value <= 0
}
</script>

<style scoped>
/* Reset some default styles */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
  background-color: #f4f4f4;
}

/* Basic styling for the product container */
.product {
  display: flex;
  max-width: 1000px;
  max-height: max-content;
  margin: 20px auto;
  background-color: #fff;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.product-image {
  width: 40%;
  object-fit: cover;
  margin: 20px;
}

.product-details {
  padding: 20px;
  width: 60%;
}

/* Style for the book title */
.title {
  font-size: 28px;
  margin-bottom: 10px;
  color: #333;
}

/* Style for the book author */
.author {
  font-style: italic;
  color: #666;
  margin-bottom: 15px;
}

/* Style for the book description */
.description {
  margin-bottom: 15px;
  color: #444;
}

/* Style for the book price */
.price {
  font-weight: bold;
  margin-bottom: 20px;
  color: #333;
}

/* Style for the quantity section */
.quantity-section {
  margin-bottom: 15px;
}

/* Style for the quantity input */
.quantity-input {
  padding: 8px;
  width: 60px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

/* Style for the "Add to Cart" button */
.add-to-cart-btn {
  padding: 12px 24px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 18px;
  transition: background-color 0.3s ease;
}

.add-to-cart-btn:hover {
  background-color: #0056b3;
}

/* CSS cho div có class "check_quantity" */
.check_quantity {
  background-color: #ffcccc;
  color: #ff0000;
  padding: 5px 10px;
  border-radius: 4px;
  margin-top: 10px;
  max-width: fit-content;
}
</style>
