<template>
  <Header />
  <div class="home">
    <h1>Danh sách đơn hàng</h1>
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>User ID</th>
          <th>Fullname</th>
          <th>address</th>
          <th>Order Date</th>
          <th>Status</th>
          <th>Total Money</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="order in orders" :key="order.id">
          <td>{{ order.id }}</td>
          <td>{{ order.user_id }}</td>
          <td>{{ order.fullname }}</td>
          <td>{{ order.address }}</td>
          <td>{{ formatOrderDate(order.order_date) }}</td>
          <td>
            <select v-model="order.status" @change="updateStatus(order)">
              <option value="pending">Pending</option>
              <option value="processing">Processing</option>
              <option value="shipped">Shipped</option>
              <option value="delivered">Delivered</option>
              <option value="cancelled">Cancelled</option>
            </select>
          </td>
          <td>{{ order.total_money }}</td>
        </tr>
      </tbody>
    </table>
  </div>
  <Footer />
</template>

<script setup>
import { ref, onMounted } from 'vue'

import Header from '@/components/Header.vue'
import Footer from '@/components/Footer.vue'

const orders = ref([])

onMounted(async () => {
  await fetchOrders()
})

async function fetchOrders() {
  try {
    const response = await fetch('http://localhost:3000/api/orders/admin/getAllOrders')
    orders.value = await response.json()
  } catch (error) {
    console.error('Error fetching orders:', error)
  }
}

function formatOrderDate(dateString) {
  const date = new Date(dateString)
  return date.toLocaleString()
}

async function updateStatus(order) {
  try {
    const response = await fetch(
      `http://localhost:3000/api/orders/admin/getAllOrders/${order.id}`,
      {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ status: order.status })
      }
    )

    if (response.ok) {
      console.log(`Order ${order.id} updated successfully!`)
      // Cập nhật trạng thái ngay trong danh sách orders
      const updatedOrderIndex = orders.value.findIndex((o) => o.id === order.id)
      if (updatedOrderIndex !== -1) {
        orders.value[updatedOrderIndex].status = order.status
      }
    } else {
      console.error('Failed to update order status')
    }
  } catch (error) {
    console.error('Error updating order status:', error)
  }
}
</script>


<style>
/* CSS cho trang quản lý đơn hàng */
/* Thiết lập font và margin, padding mặc định */
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
}

/* Định dạng phần tiêu đề */
h1 {
  text-align: center;
  margin-bottom: 20px;
}

/* Định dạng bảng */
table {
  width: 100%;
  border-collapse: collapse;
}

/* Định dạng header của bảng */
th {
  background-color: #f2f2f2;
  padding: 8px;
  text-align: left;
}

/* Định dạng dòng trong bảng */
td {
  border-bottom: 1px solid #ddd;
  padding: 8px;
}

/* Định dạng màu nền cho các dòng chẵn */
tbody tr:nth-child(even) {
  background-color: #f9f9f9;
}

.home {
  height: 100vh;
  width: calc(100% - 240px);
  position: relative;
  left: 240px;
  background: var(--body-color);
  transition: var(--tran-05);
}

.sidebar.close ~ .home {
  width: calc(100% - 88px);
  left: 88px;
}
</style>