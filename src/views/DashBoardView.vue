<template>
  <div>
    <h1>註冊</h1>
    <input type="text" placeholder="Email" v-model="signUpData.email" />
    <input type="text" placeholder="Password" v-model="signUpData.password" />
    <input type="text" placeholder="Nickname" v-model="signUpData.nickname" />
    <button type="button" @click="signUp">SignUp</button>
    <br>
    <div class="status-success" v-if="signUpStatus === STATUS.success">
      <p>註冊成功！</p>
    </div>
    <div class="status-error" v-if="signUpStatus === STATUS.error">
      <p>註冊失敗：{{ signUpInfo }}</p>
    </div>
    <h1>登入</h1>
    <input type="text" placeholder="Email" v-model="signInData.email" />
    <input type="text" placeholder="Password" v-model="signInData.password" />
    <button type="button" @click="signIn">SignIn</button>
    <br>
    <div class="status-success" v-if="signInStatus === STATUS.success">
      <p>登入成功！</p>
      <p>uid: {{ logInToken }}</p>
    </div>
    <div class="status-error" v-if="signInStatus === STATUS.error">
      <p>登入失敗：{{ signInInfo }}</p>
    </div>
    <h1>驗證</h1>
    <button v-if="isLoggedIn" type="button" @click="authenticate">CheckOut</button>
    <div v-else="isLoggedIn">尚未登入</div>
    <div class="status-success" v-if="authenticateStatus === STATUS.success">
      <p>驗證成功！</p>
    </div>
    <div class="status-error" v-if="authenticateStatus === STATUS.error">
      <p>驗證失敗: {{ authenticateInfo }}</p>
    </div>
    <!-- <h1>登出</h1>
    <button v-if="isLoggedIn" type="button">SignOut</button>
    <div v-else="isLoggedIn">尚未登入</div> -->

  </div>
</template>

<script setup>

import { ref, computed, onMounted } from 'vue'
import axios from 'axios';


const signUpData = ref({
  email: '',
  password: '',
  nickname: ''
});

const STATUS = {
  idle: 'idle',
  success: 'success',
  error: 'error',
}
const signUpStatus = ref(STATUS.idle);
const signUpInfo = ref("");

const signInData = ref({
  email: '',
  password: ''
});
const signInStatus = ref(STATUS.idle);
const signInInfo = ref("");
const logInToken = ref("");

const isLoggedIn = computed(() => {
  return logInToken.value !== '';
});

const authenticateStatus = ref(STATUS.idle);
const authenticateInfo = ref("");

// https://todolist-api.hexschool.io
const API_URL = 'https://todolist-api.hexschool.io';
const API_CMD = {
  POST: {
    signUp: `${API_URL}/users/sign_up`,
    signIn: `${API_URL}/users/sign_in`,
    signOout: `${API_URL}/users/sign_out`,
  },
  GET: {
    checkOut: `${API_URL}/users/checkout`,
  },
}

const signUp = async () => {
  try {
    console.log("do sign up")
    const response = await axios.post(API_CMD.POST.signUp, signUpData.value);
    console.log(response)
    // signUpInfo.value = response.data.message || '註冊成功';
    signUpStatus.value = STATUS.success;

  } catch (error) {
    console.error('Sign Up Error:', error);
    signUpInfo.value = error.response ? (
      error.response.data.message instanceof Array ?
        error.response.data.message.join(', ') :
        error.response.data.message
    ) : '未知錯誤';
    signUpStatus.value = STATUS.error;
  }
};

const signIn = async () => {
  try {
    console.log("do sign in")
    const response = await axios.post(API_CMD.POST.signIn, signInData.value);
    console.log(response)
    // 處理登入成功的邏輯
    signInStatus.value = STATUS.success;
    console.log(response)
    logInToken.value = response.data.token || '未提供 UID';
    document.cookie = `todoToken=${response.data.token};path=/;`;
  } catch (error) {
    console.error('Sign In Error:', error);
    // 處理登入失敗的邏輯
    signInInfo.value = error.response ? (
      error.response.data.message instanceof Array ?
        error.response.data.message.join(', ') :
        error.response.data.message
    ) : '未知錯誤';
    signInStatus.value = STATUS.error;
  }
}

const authenticate = async () => {
  try {
    console.log("do authenticate");
    const response = await axios.get(API_CMD.GET.checkOut, {
      headers: {
        Authorization: logInToken.value
      }
    });
    console.log(response);
    authenticateStatus.value = STATUS.success;

  } catch (error) {
    console.error('Authentication Error:', error);
    // 處理驗證失敗的邏輯
    authenticateInfo.value = error.response ? (
      error.response.data.message instanceof Array ?
        error.response.data.message.join(', ') :
        error.response.data.message
    ) : '未知錯誤';
    authenticateStatus.value = STATUS.error;
  }
}

onMounted(() => {
  // 檢查是否已登入
  const token = document.cookie.split('; ').find(row => row.startsWith('todoToken='));
  if (token) {
    logInToken.value = token.split('=')[1];
    console.log("已登入，UID:", logInToken.value);
  }
});

</script>

<style scoped>
.status-success {
  color: green;
}

.status-error {
  color: red;
}
</style>
