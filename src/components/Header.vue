<template>
  <nav class="menu">
    <ul>
      <li v-if="shouldShowLink" style="color: #fff" class="bold-text">
        <router-link class="no-underline" :to="{ name: 'Home' }"> Trang Chủ</router-link>
      </li>
      <li v-else style="color: #fff" class="bold-text">
        <router-link class="no-underline" :to="{ name: 'admin' }">Sản phẩm </router-link>
      </li>

      <li>
        <a class="bold-text" href="#">Thương hiệu</a>
        <ul class="sub-menu">
          <li><a href="#">Burberry</a></li>
          <li><a href="#">ESCADA</a></li>
          <li><a href="#">CAROLINA HERRERA</a></li>
        </ul>
      </li>
      <li><a class="bold-text" href="#">Giới thiệu</a></li>
      <li><a class="bold-text" href="#">Liên hệ</a></li>
      <li class="login" v-if="isLoggedIn == 0">
        <a href="#">
          <router-link class="child" :to="{ name: 'login' }">
            <!-- <i>Đăng nhập</i> -->
            Đăng nhập
          </router-link></a
        >
      </li>
      <button class="custom-button" v-else @click="logout()">Đăng Xuất</button>
      <li class="profile" v-if="isLoggedIn == 1">
        <a href="#"
          ><router-link :to="{ name: 'user.home' }"><span>Profile</span></router-link></a
        >
      </li>
      <li class="orders" v-if="isLoggedIn == 1">
        <a href="#"
          ><router-link v-if="!shouldShowLink" :to="{ name: 'admin.orders' }"
            ><span>Đơn hàng</span></router-link
          >
          <router-link v-else :to="{ name: 'myOrders' }"><span>Đơn hàng</span></router-link>
        </a>
      </li>
      <li class="cart" v-if="isLoggedIn == 1 && shouldShowLink">
        <a href="#"
          ><router-link :to="{ name: 'cart' }"><span>Giỏ Hàng</span></router-link></a
        >
      </li>
    </ul>
  </nav>
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
  localStorage.setItem('isLoggedIn', 0)
  router.push({ name: 'login' })
}
</script>
<style scoped>
/* Reset some default styles */
* {
  margin: 0;
  padding: 0;
  list-style: none;
}

/* Style for the main menu */
.menu ul {
  background: #333;
  padding: 20px;
}

.menu ul li {
  position: relative;
  display: inline-block;
}

.menu ul li a {
  display: block;
  padding: 10px 20px;
  color: #fff;
  text-decoration: none;
}

.menu ul li:hover > ul {
  display: block;
}

/* Style for the sub-menu */
.menu ul ul {
  display: none;
  position: absolute;
  top: 100%;
  left: 0;
  background: #444;
}

.menu ul ul li {
  display: block;
}

.menu ul ul li a {
  padding: 10px 30px;
}

.menu ul ul ul {
  top: 0;
  left: 100%;
  background: #555;
}

.menu ul li.login {
  float: right;
}

.menu ul li.login a {
  background: #ffcc00; /* Màu nền của nút đăng nhập */
  /* padding: 10px 20px; */

  color: #333;
}

.menu ul li.login a:hover {
  background: #ffdb4d; /* Màu nền khi rê chuột qua nút đăng nhập */
}

.menu ul li.profile {
  float: right;
}

.menu ul li.profile a {
  background: #336699; /* Màu nền của nút profile */
  padding: 10px 20px;
  color: #fff;
}

.menu ul li.profile a:hover {
  background: #4477b2; /* Màu nền khi rê chuột qua nút profile */
}

.no-underline {
  text-decoration: none;
}

.bold-text {
  font-weight: bold;
}

.child {
  width: 100%; /* Phần tử con có chiều rộng bằng với phần tử cha */
  height: 100%; /* Phần tử con có chiều cao bằng với phần tử cha */
}

.menu ul li.cart {
  float: right;
}

.menu ul li.cart a {
  background: #ff6600; /* Màu nền của nút giỏ hàng */
  padding: 10px 20px;
  color: #fff;
}

.menu ul li.cart a:hover {
  background: #ff8533; /* Màu nền khi rê chuột qua nút giỏ hàng */
}

.custom-button {
  padding: 10px 20px; /* Kích thước của button */
  background-color: #3498db; /* Màu nền của button */
  color: #fff; /* Màu chữ */
  border: none; /* Xóa viền */
  border-radius: 5px; /* Bo tròn góc */
  cursor: pointer; /* Biến con trỏ thành hình bàn tay khi di chuột vào */
  font-size: 16px; /* Kích thước chữ */
}

.custom-button:hover {
  background-color: #2980b9; /* Màu nền khi rê chuột qua */
}

.menu ul {
  display: flex; /* Sử dụng flexbox */
  justify-content: flex-end; /* Đặt các phần tử về phía bên phải */
}

.menu ul li {
  margin-left: 10px; /* Khoảng cách giữa các mục */
}

.menu ul li.orders {
  float: right;
}

.menu ul li.orders a {
  background: #ff9900; /* Màu nền của mục quản lý đơn hàng */
  padding: 10px 20px;
  color: #fff;
}

.menu ul li.orders a:hover {
  background: #ffad33; /* Màu nền khi rê chuột qua mục quản lý đơn hàng */
}
</style>