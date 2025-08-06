<template>
    <div>
        <h1>註冊</h1>
        <input type="text" placeholder="Email" v-model="signUpData.email" />
        <input type="text" placeholder="Password" v-model="signUpData.password" />
        <input type="text" placeholder="Nickname" v-model="signUpData.nickname" />
        <button type="button">SignUp</button>
        <h1>登入</h1>
        <input type="text" placeholder="Email" v-model="signInData.email" />
        <input type="text" placeholder="Password" v-model="signInData.password" />
        <button type="button">SignIn</button>
        <h1>驗證</h1>
        <button v-if="isLoggedIn" type="button">CheckOut</button>
        <div v-else="isLoggedIn">尚未登入</div>
        <h1>登出</h1>
        <button v-if="isLoggedIn" type="button">SignOut</button>
        <div v-else="isLoggedIn">尚未登入</div>

    </div>
</template>

<script setup>

import { ref, computed } from 'vue'

const signUpData = ref({
    email: '',
    password: '',
    nickname: ''
});

const signInData = ref({
    email: '',
    password: ''
});

const isLoggedIn = computed(() => {
    // return !!localStorage.getItem('token');
    return true;
});

</script>


<style scoped></style>
