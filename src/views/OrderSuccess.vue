<template>
  <div class="order-details">
    <h1>Chi tiết đơn hàng</h1>
    <div class="order-info">
      <p><strong>ID đơn hàng:</strong> DH123456</p>
      <p><strong>Ngày đặt hàng:</strong> 01/12/2023</p>
      <p><strong>Trạng thái:</strong> Đang xử lý</p>
      <p><strong>Người nhận:</strong> Nguyễn Văn A</p>
      <p><strong>Số điện thoại:</strong> 0123456789</p>
    </div>
    <div class="product-details">
      <h2>Chi tiết sản phẩm</h2>
      <table>
        <thead>
          <tr>
            <th>Sản phẩm</th>
            <th>Số lượng</th>
            <th>Giá</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>Iphone 13</td>
            <td>2</td>
            <td>$1200</td>
          </tr>
          <tr>
            <td>Áo thun nam</td>
            <td>3</td>
            <td>$25</td>
          </tr>
          <!-- Thêm các dòng sản phẩm khác tương tự -->
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { useRoute } from 'vue-router'
import { ref } from 'vue'

const route = useRoute()
const parsedData = ref({})
// Thực hiện việc truy cập dữ liệu từ query params
const orderData = route.query.orderData

// Kiểm tra và xử lý dữ liệu
if (orderData) {
  try {
    parsedData.value = JSON.parse(orderData)
    console.log(parsedData.value) // Sử dụng dữ liệu đã được parse
  } catch (error) {
    console.error('Error parsing orderData:', error)
  }
}
</script>


<style>
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
}

.order-details {
  width: 80%;
  margin: 20px auto;
  border: 1px solid #ccc;
  padding: 20px;
}

.order-info p {
  margin: 5px 0;
}

.product-details table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

.product-details th,
.product-details td {
  border: 1px solid #ccc;
  padding: 8px;
  text-align: left;
}

.product-details th {
  background-color: #f2f2f2;
}
</style>