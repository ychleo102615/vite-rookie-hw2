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
            <p>註冊失敗：{{ singUpInfo }}</p>
        </div>
        <h1>登入</h1>
        <input type="text" placeholder="Email" v-model="signInData.email" />
        <input type="text" placeholder="Password" v-model="signInData.password" />
        <button type="button" @click="signUpAno">SignIn</button>
        <h1>驗證</h1>
        <button v-if="isLoggedIn" type="button">CheckOut</button>
        <div v-else="isLoggedIn">尚未登入</div>
        <h1>登出</h1>
        <button v-if="isLoggedIn" type="button">SignOut</button>
        <div v-else="isLoggedIn">尚未登入</div>

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
const singUpInfo = ref("");

const signInData = ref({
    email: '',
    password: ''
});

const isLoggedIn = computed(() => {
    // return !!localStorage.getItem('token');
    return true;
});

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
        singUpInfo.value = error.response ? (
            error.response.data.message instanceof Array ?
                error.response.data.message.join(', ') :
                error.response.data.message
        ) : '未知錯誤';
        signUpStatus.value = STATUS.error;
    }
};

</script>

<style scoped>
.status-success {
    color: green;
}

.status-error {
    color: red;
}
</style>
