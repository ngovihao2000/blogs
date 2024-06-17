<script setup>
import { RouterLink } from "vue-router";
import { storeToRefs } from "pinia";
import { useBlogStore } from "@/stores/blogStore";
import { ref } from "vue";

const { myInfo } = storeToRefs(useBlogStore());

const isBurgerOpen = ref(false);

function openBurgerMenu() {
  isBurgerOpen.value = !isBurgerOpen.value;
}
// register
const showModal = ref(false);
const showLoginModal = ref(false);
const showRegisterModal = ref(false);
const fullname = ref('');
const email = ref('');
const password = ref('');
const passwordConfirmation = ref('');
const loginUsername = ref('');
const loginPassword = ref('');
const isLoggedIn = ref(false);
const fullnameError = ref('');
const emailError = ref('');
const passwordError = ref('');
const passwordConfirmationError = ref('');
const loginUsernameError = ref('');
const loginPasswordError = ref('');

const closeModalOnClickOutside = (event) => {
  if (!event.target.closest('.form') && !event.target.closest('.register-info')) {
    showModal.value = false;
    showLoginModal.value = false;
    showRegisterModal.value = false;
  }
};

const login = () => {
  validateLoginForm();

  // Lấy thông tin đăng nhập từ localStorage
  const storedUsername = localStorage.getItem('username');
  const storedPassword = localStorage.getItem('password');

  // Lấy thông tin đăng nhập từ form
  const username = loginUsername.value;
  const password = loginPassword.value;

  // Kiểm tra thông tin đăng nhập
  if (!username.trim() || !password.trim()) {
    alert('Vui lòng nhập đầy đủ tài khoản và mật khẩu');
    return;
  }
  if (username === storedUsername && password === storedPassword) {
    showToastMessage('Đăng nhập thành công!');
    showLoginModal.value = false;
    isLoggedIn.value = true;
  } else {
    showToastMessage('Sai tên đăng nhập hoặc mật khẩu. Vui lòng nhập lại.');
  }
};

const validateRegisterForm = () => {
  fullnameError.value = '';
  emailError.value = '';
  passwordError.value = '';
  passwordConfirmationError.value = '';

  if (!fullname.value.trim()) {
    fullnameError.value = 'Vui lòng nhập tên đầy đủ';
  }
  const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!email.value.trim()) {
    emailError.value = 'Vui lòng nhập email';
  } else if (!emailPattern.test(email.value)) {
    emailError.value = 'Email không hợp lệ';
  }
  if (!password.value.trim()) {
    passwordError.value = 'Vui lòng nhập mật khẩu';
  } else if (password.value.length < 6) {
    passwordError.value = 'Mật khẩu phải chứa ít nhất 6 kí tự';
  }
  if (passwordConfirmation.value !== password.value) {
    passwordConfirmationError.value = 'Mật khẩu nhập lại không khớp';
  }
};

const register = () => {
  validateRegisterForm();
  if (!fullnameError.value && !emailError.value && !passwordError.value && !passwordConfirmationError.value) {
    showToastMessage('Đăng ký thành công!');
    // Lưu thông tin đăng nhập vào localStorage
    localStorage.setItem('username', email.value);
    localStorage.setItem('password', password.value);
    // Cập nhật thông tin đăng nhập cho form đăng nhập
    loginUsername.value = email.value;
    loginPassword.value = password.value;

    fullname.value = '';
    email.value = '';
    password.value = '';
    passwordConfirmation.value = '';
    showModal.value = false;
    isLoggedIn.value = true;
    showRegisterModal.value = false; 
  }
};

const validateLoginForm = () => {
  loginUsernameError.value = '';
  loginPasswordError.value = '';
  if (!loginUsername.value.trim()) {
    loginUsernameError.value = 'Vui lòng nhập tên đăng nhập';
  }
  if (!loginPassword.value.trim()) {
    loginPasswordError.value = 'Vui lòng nhập mật khẩu';
  }
  if (!loginPassword.value.trim() && !loginUsername.value.trim()) {
    alert("Vui lòng nhập đầy đủ tài khoản và mật khẩu")
  }
};

const clearError = (field) => {
  if (field === 'fullname') fullnameError.value = '';
  if (field === 'email') emailError.value = '';
  if (field === 'password') passwordError.value = '';
  if (field === 'passwordConfirmation') passwordConfirmationError.value = '';
  if (field === 'loginUsername') loginUsernameError.value = '';
  if (field === 'loginPassword') loginPasswordError.value = '';
};

const showToastMessage = (message) => {
  alert(message);
};

const logout = () => {
  isLoggedIn.value = false;
  showRegisterModal.value = false;
  showLoginModal.value = false;
  loginUsername.value = '';
  loginPassword.value = '';
};
</script>

<template>
  <nav class="navbar is-fixed-top" role="navigation" aria-label="main navigation">
    <div class="navbar-brand" style="justify-content: space-between;">
      <div class="navbar-item">
        <img src="/public/Ten-truong-do-1000x159.png" alt="Logo" style="max-height: 50px;">
      </div>
      <div class="navbar-item" >
       <div>
        <p>GV trở giảng: THs.Phạm Đẳng Phương</p>
       </div>
      </div>
    </div>
    <div class="navbar-brand">
      <!-- Centered content -->
      <div class="navbar-item has-text-centered">
        <RouterLink to="/" class="navbar-item is-size-3">
          {{ myInfo.blogTitle }}
        </RouterLink>
      </div>

      <!-- Burger menu button -->
      <a
        role="button"
        class="navbar-burger"
        aria-label="menu"
        aria-expanded="false"
        data-target="navbarBlogMenu"
        active-class="is-active"
        @click="openBurgerMenu"
        :class="{ 'is-active': isBurgerOpen }"
      >
        <span aria-hidden="true"></span>
        <span aria-hidden="true"></span>
        <span aria-hidden="true"></span>
      </a>
      <div id="navbarBlogMenu" class="navbar-menu" :class="{ 'is-active': isBurgerOpen }">
      <div class="navbar-start"></div>
      <div class="navbar-end">
        <div class="navbar-item">
          <RouterLink
            @click="isBurgerOpen = false"
            to="/"
            class="navbar-item"
            active-class="is-active"
          >
            Trang chủ
          </RouterLink>
          <RouterLink
            @click="isBurgerOpen = false"
            to="/blogs"
            class="navbar-item"
            active-class="is-active"
          >
            Danh Sách Blog
          </RouterLink>
          <RouterLink
            @click="isBurgerOpen = false"
            to="/tags"
            class="navbar-item"
            active-class="is-active"
          >
            Tags
          </RouterLink>
          <RouterLink
            @click="isBurgerOpen = false"
            to="/links"
            class="navbar-item"
            active-class="is-active"
          >
            Các trang web giải trí
          </RouterLink>
        <div class="nav_login"  @click="showLoginModal = true" v-if="!isLoggedIn">
          <p >Đăng Nhập</p>
        </div>
        <div class="nav_register" @click="showRegisterModal = true" v-if="!isLoggedIn">
          <p>Đăng Ký </p>
        </div>
        <div class="nav_register" @click="logout" v-else>
          <p class="nav_register_first">{{ loginUsername }}</p>
          <p class="nav_register_last" @click="logout">Logout</p>
        </div>
        </div>
      </div>
    </div>
    </div>
  </nav>
  <div class="modal" v-if="showRegisterModal" @click="closeModalOnClickOutside">
    <div class="modal_main">
      <form action="" method="POST" class="form" id="register-form" @submit.prevent="register">
        <div class="modal_close"  @click="showRegisterModal = false"><i class="fa-solid fa-x"></i></div>
        <h3 class="heading">Đăng kí</h3>
        <div class="spacer"></div>
        <div class="form-group">
          <label for="fullname" class="form-label">Họ và Tên</label>
          <input v-model="fullname" @focus="clearError('fullname')" id="fullname" name="fullname" type="text" placeholder="Ngô Vĩ Hào" class="form-control">
          <span class="form-message" v-if="fullnameError">{{ fullnameError }}</span>
        </div>
        <div class="form-group">
          <label for="email" class="form-label">Email</label>
          <input v-model="email" @focus="clearError('email')" id="email" name="email" type="text" placeholder="ngovihao2000@gmail.com" class="form-control">
          <span class="form-message" v-if="emailError">{{ emailError }}</span>
        </div>
        <div class="form-group">
          <label for="password" class="form-label">Mật khẩu</label>
          <input v-model="password" @focus="clearError('password')" id="password" name="password" type="password" placeholder="Nhập mật khẩu" class="form-control">
          <span class="form-message" v-if="passwordError">{{ passwordError }}</span>
        </div>
        <div class="form-group">
          <label for="password_confirmation" class="form-label">Nhập lại mật khẩu</label>
          <input v-model="passwordConfirmation" @focus="clearError('passwordConfirmation')" id="password_confirmation" name="password_confirmation" placeholder="Nhập lại mật khẩu" type="password" class="form-control">
          <span class="form-message" v-if="passwordConfirmationError">{{ passwordConfirmationError }}</span>
        </div>
        <button type="submit" class="form_btn">Đăng ký</button>
      </form>
    </div>
  </div>
  <div class="modal_login" v-if="showLoginModal" @click="closeModalOnClickOutside">
    <div class="modal_main_login">
      <form @submit.prevent="login" class="form" id="login-form">
        <div class="modal_close_login" @click="showLoginModal = false"><i class="fa-solid fa-x" ></i></div>
        <h3 class="heading">Đăng Nhập</h3>
        <div class="spacer"></div>
        <div class="form-group">
          <label for="username" class="form-label">Tên Đăng Nhập</label>
          <input v-model="loginUsername" @focus="clearError('loginUsername')" id="username" type="text" placeholder="ngovihao2000@gmail.com" class="form-control">
          <span class="form-message" v-if="loginUsernameError">{{ loginUsernameError }}</span>
        </div>
        <div class="form-group">
          <label for="password_login" class="form-label">Mật khẩu</label>
          <input v-model="loginPassword" @focus="clearError('loginPassword')" id="password_login" type="password" placeholder="Nhập mật khẩu" class="form-control">
          <span class="form-message" v-if="loginPasswordError">{{ loginPasswordError }}</span>
        </div>
        <button type="submit" class="form_btn">Đăng Nhập</button>
      </form>
    </div>
  </div>
</template>
<style scoped>
.navbar {
  flex-direction: column;
}

.header {
  display: flex;
  width: 100%;
}

.header .navbar-item .navbar-item1{
  flex-grow: 1;
}
.nav_login{
  padding-right: 6px;
  border-right: 1px solid #000000;
}
.nav_login:hover,.nav_register:hover{
  color: hsl(229deg, 53%, 53%);
  cursor: pointer;
}
.nav_register{
  padding-left: 6px;
}
/* register */
.modal ,.modal_login{
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background-color: rgba(0,0,0,0.4);
  z-index: 3;
  display: block;
  align-items: center;
  justify-content: center;
}
.modal.open, .modal_login.open{
    display: flex;
}
.modal_main,.modal_main_login {
  display: flex;
  justify-content: center;
  font-family: "Open Sans", sans-serif;
  z-index: 10;
}
.modal_login .modal_close_login{
  top: 0%;
}
.modal_close_login i {
  color:#000000;
}
.modal_close,.modal_close_login{
  font-size: 18px;
  cursor: pointer;
  right: 36%;
  padding: 6px;
  position: absolute;
  top: 0%;
  right: 1%;
}
.modal_close i {
  color: #000000;
}
.modal_close:hover{
  opacity: 0.7;
}
.form {
  animation: modalFadeIn ease .5s;
  border-radius: 6px;
  width: 56vh;;
  min-height: 100px;
  padding: 32px 24px;
  text-align: center;
  background: #fff;
  margin: 24px;
  align-self: center;
  box-shadow: 0 2px 5px 0 rgba(51, 62, 73, 0.1);
  z-index: 100000000000000000;
  margin-top: 14%;
  position: relative;
  overflow: hidden;
}
.form .heading {
  font-size: 2rem;
  color: #000000;
}
.form-group {
  display: flex;
  margin-bottom: 8px;
  flex-direction: column;
}
.form input ,.form-label {
  color: #000000;
}
.form-label,
.form-message {
  text-align: left;
}
.form-message {
  color:#d63031;
}
.form-label {
  font-weight: 700;
  padding-bottom: 6px;
  line-height: 1.8rem;
  font-size: 1rem;
}

.form-control {
  height: 40px;
  padding: 8px 12px;
  border: 1px solid #b3b3b3;
  border-radius: 3px;
  outline: none;
  font-size: 1rem;
}

.form-control:hover {
  border-color: #1dbfaf;
}

.form-group.invalid .form-control {
  border-color: #f33a58;
}

.form-group.invalid .form-message {
  color: #f33a58;
}

.form-message {
  font-size: 1rem;
  line-height: 1.5rem;
  padding: 6px 0 0;
}

/* .form-submit:hover {
  background-color: #1ac7b6;
} */
.spacer {
  margin-top: 12px;
}

.v-toolbar__content{
  z-index: 10000;
}
@keyframes modalFadeIn {
from{
    opacity: 0;
    transform: translateY(-100px);
}
to{
    transform: translateY(0);
    opacity: 1;
}
}

.form_btn  {
  margin: 12px 0;  
  width: 100% !important; 
  background: #5E5DF0;
  border-radius: 999px;
  box-shadow: #5E5DF0 0 10px 20px -10px;
  box-sizing: border-box;
  color: #FFFFFF;
  cursor: pointer;
  font-size: 25px;
  font-weight: 300;
  line-height: 20px;
  opacity: 1;
  outline: 0 solid transparent;
  padding: 20px 30px;
  user-select: none;

  width: fit-content;
  word-break: break-word;
  border: 0;
}
/* logout */
.nav_register{
  display: flex;
}
.nav_register_first{
  padding-right: 6px;
  border-right: 1px solid #000000;
}
.nav_register_last{
  padding-left: 6px;
}
</style>
