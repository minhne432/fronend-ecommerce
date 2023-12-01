<template>
  <header class="header">
    <div class="container">
      <h1 class="logo">Bookstore</h1>
      <nav class="navigation">
        <ul>
          <router-link v-if="shouldShowLink" :to="{ name: 'Home' }">
            <li><span>Shop</span></li>
          </router-link>

          <li class="dropdown">
            <span>User</span>
            <div class="dropdown-content">
              <router-link :to="{ name: 'cart' }"><span>Cart</span> </router-link>
              <router-link :to="{ name: 'user.home' }"><span>Profile</span> </router-link>
              <router-link :to="{ name: 'myOrders' }"><span>Orders</span> </router-link>

              <li v-if="isLoggedIn">
                <button @click="logout()">
                  <i class="bx bx-log-out icon"></i>
                  <span>Logout</span>
                </button>
              </li>

              <li v-if="!isLoggedIn">
                <router-link :to="{ name: 'login' }">
                  <i class="bx bx-log-in icon"></i>
                  <span class="text nav-text">Login</span>
                </router-link>
              </li>
            </div>
          </li>
        </ul>
      </nav>
    </div>
  </header>
</template>
<script setup>
import { computed, ref } from 'vue'
import { useRouter } from 'vue-router'
const router = useRouter()

const shouldShowLink = computed(() => {
  const role_id = localStorage.getItem('role_id') != 2 ? true : false
  return role_id
})

const isLoggedIn = ref(localStorage.getItem('isLoggedIn'))

const logout = () => {
  localStorage.removeItem('access_token')
  localStorage.removeItem('fullname')
  localStorage.removeItem('id')
  localStorage.removeItem('phone_number')
  localStorage.removeItem('address')
  localStorage.removeItem('role_id')
  localStorage.removeItem('isLoggedIn')
  router.push({ name: 'Home' })
}
</script>
<style scoped>
/* Reset CSS */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* Style cho header */
.header {
  background-color: #333; /* Màu nền */
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1); /* Hiệu ứng bóng đổ */
  padding: 10px 0; /* Khoảng cách đệm */
}

.container {
  width: 90%; /* Độ rộng của header */
  margin: 0 auto; /* Canh giữa */
  display: ;
  justify-content: space-between; /* Căn giữa các phần tử */
  align-items: center;
}

.logo {
  font-size: 24px; /* Cỡ chữ */
  margin: 0; /* Loại bỏ khoảng cách */
  color: #fcfbfb; /* Màu chữ */
}

.navigation ul {
  list-style: none; /* Loại bỏ dấu đầu dòng */
  margin-left: 80%;
  padding: 0;
  display: flex;
  align-items: center;
}

.navigation ul li {
  margin-left: 20px; /* Khoảng cách giữa các phần tử */
  position: relative; /* Để dropdown hoạt động */
}

.navigation ul li:last-child {
  margin-right: 0; /* Loại bỏ khoảng cách ở phần tử cuối cùng */
}

/* Style cho dropdown */
.dropdown {
  position: relative;
  display: inline-block;
}

.dropdown-content {
  display: none;
  position: absolute;
  background-color: #333;
  min-width: 160px;
  box-shadow: 0px 8px 16px 0px rgba(0, 0, 0, 0.2);
  z-index: 1;
  top: 100%; /* Đặt dropdown-content xuống dưới dropdown */
  left: 0; /* Đặt dropdown-content bên trái của dropdown */
}

.dropdown:hover .dropdown-content {
  display: block;
}

.dropdown-content a {
  color: #fff;
  padding: 12px 16px;
  text-decoration: none;
  display: block;
}

.dropdown-content li button {
  color: #fff;
  background-color: #333;
  border: none;
  padding: 12px 16px;
  text-decoration: none;
  display: block;
  cursor: pointer;
  transition: background-color 0.3s ease;
  max-width: 100%;
}

.dropdown-content li button:hover {
  background-color: #000;
}
.dropdown-content a:hover {
  background-color: #000;
}

.dropdown:hover .dropdown-content {
  display: block;
}
/* Phần CSS khác */
/* CSS cho phần chuyển hướng đến Shop */
.navigation ul li router-link[to="{ name: 'Home' }"] {
  /* Thêm kiểu dáng */
  position: relative;
}

.navigation ul li router-link[to="{ name: 'Home' }"]::before {
  /* Hiệu ứng underline */
  content: '';
  position: absolute;
  bottom: -3px; /* Khoảng cách từ chữ đến đường underline */
  left: 0;
  width: 100%;
  height: 2px;
  background-color: #007bff; /* Màu underline */
  transition: width 0.3s ease;
  transform: scaleX(0); /* Bắt đầu với độ rộng là 0 */
}

.navigation ul li router-link[to="{ name: 'Home' }"]:hover::before {
  /* Hiển thị underline khi hover */
  transform: scaleX(1); /* Mở rộng underline */
}

span {
  color: #fff;
}

ul {
  margin-left: 20px; /* Di chuyển sang bên trái 20px */
  /* Hoặc có thể sử dụng giá trị âm để di chuyển sang bên trái */
  /* margin-left: -20px; */
}
</style>