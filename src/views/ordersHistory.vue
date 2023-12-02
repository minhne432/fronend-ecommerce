<template>
  <Header />
  <div class="order-history">
    <h1>Lịch sử đơn hàng</h1>

    <div class="order">
      <div class="order-header">
        <span>ID đơn hàng</span>
        <span>Thời gian đặt hàng</span>
        <span>Trạng thái</span>

        <span>Số điện thoại</span>
        <span>Tổng tiền</span>
        <span>Chi tiết sản phẩm</span>
      </div>

      <div class="order-details">
        <div class="order-info" v-for="order in orders" :key="order.id">
          <span>#{{ order.id }}</span>
          <span>{{ formatOrderDate(order.order_date) }}</span>
          <span>{{ order.status }}</span>

          <span>{{ order.phone_number }}</span>
          <span>{{ order.total_money }}</span>
          <span>
            <button @click="showOrderDetails(order.id)">Xem chi tiết sản phẩm</button>
          </span>
          <div class="overlay" v-if="showDetailsModal == true"></div>
          <div v-if="showDetailsModal" class="order-details-modal">
            <p class="centered">
              <strong>#{{ order.id }}</strong>
            </p>
            <table>
              <thead>
                <tr>
                  <th>Tên sản phẩm</th>
                  <th>Giá</th>
                  <th>Số lượng</th>
                  <!-- Các cột thông tin khác về sản phẩm -->
                </tr>
              </thead>
              <tbody>
                <tr v-for="(product, index) in orderDetails" :key="index">
                  <td>{{ product.name }}</td>
                  <td>{{ product.price }}</td>
                  <td>{{ product.number_of_products }}</td>
                  <!-- Các ô thông tin khác về sản phẩm -->
                </tr>
              </tbody>
            </table>

            <button @click="closeDetailsModal">Trở lại</button>
          </div>
        </div>

        <!-- Thêm các thông tin đơn hàng khác ở đây (có thể sử dụng thêm class "order-info" để thêm đơn hàng mới) -->
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import Header from '@/components/Header.vue'
const orders = ref([])
const userId = ref(localStorage.getItem('id')) // Thay thế userId bằng dữ liệu thực tế
const formatOrderDate = (date) => {
  return new Date(date).toLocaleString()
}

const fetchOrders = async () => {
  try {
    const response = await fetch(`http://localhost:3000/api/orders/getOrders/${userId.value}`)
    if (response.ok) {
      orders.value = await response.json()
    } else {
      console.error('Error fetching orders:', response.statusText)
    }
  } catch (error) {
    console.error('Error fetching orders:', error)
  }
}

const closeDetailsModal = () => {
  showDetailsModal.value = false
}
onMounted(fetchOrders)

const showDetailsModal = ref(false)
const orderDetails = ref([])

const showOrderDetails = async (orderId) => {
  try {
    console.log(orderId)
    const response = await fetch(`http://localhost:3000/api/orders/getOrdersDetails/${orderId}}`)
    if (response.ok) {
      orderDetails.value = await response.json()
      console.log(orderDetails.value)
      showDetailsModal.value = true
    } else {
      console.error('Error fetching order details:', response.statusText)
    }
  } catch (error) {
    console.error('Error fetching order details:', error)
  }
}
</script>

<style scoped>
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  background-color: #f4f4f4;
}

.order-history {
  width: 80%;
  margin: 20px auto;
}

h1 {
  text-align: center;
}

.order {
  background-color: #fff;
  padding: 20px;
  margin-bottom: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.order-header {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  font-weight: bold;
  border-bottom: 1px solid #ccc;
  padding-bottom: 10px;
  margin-bottom: 10px;
}

.order-header span {
  text-align: center;
}

.order-info {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
}

.order-info span {
  text-align: center;
}

.order-info ul {
  padding: 0;
}

.order-info ul li {
  list-style: none;
}

.order-details-modal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
  z-index: 9999; /* Đảm bảo modal hiển thị trên cùng */
}

/* Phần lớp phủ phía sau modal */
.overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(51, 46, 46, 0.123); /* Màu nền với độ mờ */
  z-index: 999; /* Đảm bảo lớp phủ hiển thị phía sau modal */
}

/* CSS cho bảng chi tiết sản phẩm */
.order-details-modal table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

.order-details-modal th,
.order-details-modal td {
  border: 1px solid #ccc;
  padding: 8px;
  text-align: left;
}

.order-details-modal th {
  background-color: #f2f2f2;
}

/* CSS cho nút "Trở lại" */
.order-details-modal button {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.order-details-modal button:hover {
  background-color: #0056b3;
}

.order-info button {
  padding: 8px 16px;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 14px;
  margin: 4px 2px;
  transition-duration: 0.4s;
}

.order-info button:hover {
  background-color: #45a049;
  color: white;
}

.centered {
  text-align: center;
}
</style>