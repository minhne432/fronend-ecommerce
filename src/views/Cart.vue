<template>
  <Header />

  <h1>Giỏ hàng của bạn</h1>

  <div class="cart">
    <table>
      <thead>
        <tr>
          <th>Sản phẩm</th>
          <th>Giá</th>
          <th>Số lượng</th>
          <th>Tổng</th>
        </tr>
      </thead>
      <tbody>
        <!-- Sản phẩm trong giỏ hàng -->
        <tr v-for="(product, index) in cartItems" :key="index">
          <td>{{ product.name }}</td>
          <td>{{ product.price }}</td>
          <td>{{ product.quantity }}</td>
          <td>{{ product.price * product.quantity }}</td>
          <td>
            <button class="delete-button" @click="deleteItem(product.item_id)">Remove</button>
          </td>
        </tr>
        <!-- Thêm các sản phẩm khác ở đây -->
      </tbody>
      <tfoot>
        <tr>
          <td colspan="3">Tổng cộng</td>
          <td>{{ calculateTotal() }}</td>
        </tr>
      </tfoot>
    </table>

    <div class="checkout">
      <button @click="showPurchaseModal = true">Thanh toán</button>
    </div>
  </div>

  <div v-if="showPurchaseModal" class="overlay">
    <div class="payment-form">
      <h2>Nhập thông tin thanh toán</h2>
      <form @submit.prevent="confirmPurchase">
        <label for="name">Họ và tên:</label>
        <input type="text" id="name" v-model="fullname" required /><br />

        <label for="email">Số điện thoại:</label>
        <input type="text" id="phone" v-model="phone_number" required /><br />

        <label for="address">Địa chỉ:</label>
        <input type="text" id="address" v-model="address" required /><br />

        <div class="checkout-details">
          <div class="payment-details">
            <div v-if="showPurchaseModal" class="purchase-modal">
              <div class="modal-content">
                <!-- Form thông tin mua hàng -->

                <div class="product-details-section">
                  <h3>Chi tiết sản phẩm</h3>
                  <table>
                    <thead>
                      <tr>
                        <th>Tên sản phẩm</th>
                        <th>Giá</th>
                        <th>Số lượng</th>
                        <th>Thành tiền</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(product, index) in cartItems" :key="index">
                        <td>{{ product.name }}</td>
                        <td>{{ product.price }}</td>
                        <td>{{ product.quantity }}</td>
                        <td>{{ product.price * product.quantity }}</td>
                      </tr>
                    </tbody>
                  </table>
                  <p>
                    <strong>Tổng số tiền cần thanh toán: {{ calculateTotal() }}</strong>
                  </p>
                </div>
              </div>
            </div>
          </div>
        </div>
        <button type="submit">Xác nhận mua hàng</button>
        <button @click="closePurchaseModal" class="red-button-1">Đóng</button>
      </form>
    </div>
  </div>
</template>

<script setup>
import Header from '@/components/Header.vue'
import { useRouter } from 'vue-router'
import { ref, onMounted } from 'vue'
import ordersService from '@/services/order.service.js'
const fullname = ref(localStorage.getItem('fullname'))
const phone_number = ref(localStorage.getItem('phone_number'))
const address = ref(localStorage.getItem('address'))
const user_id = ref(localStorage.getItem('id'))
// Khai báo ref để lưu trữ thông tin giỏ hàng
const cartItems = ref([])
// Khai báo data cho form mua hàng
const showPurchaseModal = ref(false)
const router = useRouter()

const calculateTotal = () => {
  let total = 0

  // Duyệt qua từng sản phẩm trong giỏ hàng và tính tổng số tiền
  for (const product of cartItems.value) {
    total += product.price * product.quantity
  }

  return total
}

// Phương thức xử lý khi người dùng xác nhận mua hàng
const confirmPurchase = async () => {
  try {
    const orderData = {
      user_id: user_id.value,
      fullname: fullname.value,
      phone_number: phone_number.value,
      address: address.value,
      items: cartItems.value.map((item) => ({
        product_id: item.product_id,
        price: item.price,
        quantity: item.quantity
      }))
    }
    console.log(orderData)
    // Gửi dữ liệu đặt hàng đến API
    const response = await ordersService.createOrder(orderData)
    response.data.order
    if (response.ok) {
      // Xử lý khi đặt hàng thành công

      // Đóng modal sau khi xác nhận mua hàng thành công
      closePurchaseModal()
      await router.push({
        name: 'order.Details',
        query: { orderData: JSON.stringify({ order: response.data.order, items: orderData }) } // Truyền dữ liệu orderData qua query params
      })
    } else {
      console.error('Error placing order:', response.message)
    }
  } catch (error) {
    console.error('Error placing order:', error)
  }
  closePurchaseModal()
}

// Phương thức để đóng modal
const closePurchaseModal = () => {
  showPurchaseModal.value = false
}

// Logic để lấy dữ liệu giỏ hàng từ service khi component được tạo
onMounted(async () => {
  try {
    const user_id = localStorage.getItem('id')
    const response = await ordersService.getCartByUserId(user_id)

    if (response.ok) {
      cartItems.value = response.data
      console.log(cartItems.value)
    } else {
      console.error('Error fetching cart details:', response.message)
    }
  } catch (error) {
    console.error('Error fetching cart details:', error)
  }
})

const deleteItem = async (item_id) => {
  try {
    const response = await ordersService.deleteCartItemById(item_id)
    if (response.ok) {
      // Xóa sản phẩm thành công từ giỏ hàng API

      // Cập nhật lại danh sách sản phẩm hiển thị trên giao diện
      cartItems.value = cartItems.value.filter((item) => item.item_id !== item_id)
    } else {
      console.error('Error deleting item:', response.message)
    }
  } catch (error) {
    console.error('Error deleting item:', error)
  }
}
</script>

<style >
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  background-color: #f4f4f4;
}

header {
  background-color: #333;
  color: white;
  text-align: center;
  padding: 20px 0;
}

.cart {
  width: 80%;
  margin: 20px auto;
  background-color: white;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

table th,
table td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: center;
}

table th {
  background-color: #f2f2f2;
}

tfoot td {
  font-weight: bold;
}

input[type='number'] {
  width: 50px;
  text-align: center;
}

.checkout {
  text-align: right;
}

button {
  padding: 10px 20px;
  background-color: #333;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #555;
}

.delete-button {
  background-color: red; /* Đặt màu nền là đỏ */
  color: white; /* Đặt màu chữ là trắng */
  border: none; /* Loại bỏ viền */
  padding: 5px 10px; /* Điều chỉnh khoảng cách giữa chữ và viền */
  border-radius: 3px; /* Bo tròn viền */
  cursor: pointer; /* Thay đổi con trỏ khi rê chuột qua nút */
}

h1,
h2 {
  color: #333;
}

/* CSS cho bảng chi tiết sản phẩm */
.books-table {
  background-color: #fff;
  border-radius: 5px;
  padding: 20px;
  margin-bottom: 20px;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th,
td {
  border-bottom: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

th {
  background-color: #f2f2f2;
}

/* CSS cho form nhập thông tin thanh toán */
.payment-form {
  background-color: #fff;
  border-radius: 5px;
  padding: 20px;
}

/* CSS cho input và label */
label {
  display: block;
  margin-bottom: 5px;
}

input[type='text'],
input[type='email'],
input[type='submit'] {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

input[type='submit'] {
  background-color: #4caf50;
  color: white;
  border: none;
  cursor: pointer;
}

input[type='submit']:hover {
  background-color: #45a049;
}

/* CSS cho lớp phủ (overlay) */
.overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5); /* Màu đen với độ mờ là 0.5 */
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 999; /* Đảm bảo lớp phủ nằm trên cùng */
}

/* CSS cho form thanh toán */
.payment-form {
  background-color: #fff;
  border-radius: 5px;
  padding: 20px;
  /* Thêm các thuộc tính sau để đặt form ở giữa màn hình */
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 1000; /* Đảm bảo form thanh toán nằm trên lớp phủ */
}
</style>
