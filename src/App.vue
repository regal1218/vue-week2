<script setup>
import axios from 'axios'
import { onMounted, ref } from 'vue'

const api = 'https://todolist-api.hexschool.io'
//註冊
const signUpData = ref({
  email: '',
  password: '',
  nickname: ''
})
const signupRes = ref('')

const signup = async () => {
  console.log(`${api}/users/sign_up`)

  try {
    const res = await axios.post(`${api}/users/sign_up`, signUpData.value)
    console.log(res)
    signupRes.value = res.data.uid
  } catch (error) {
    console.log(error)
  }
}

//登入
const signInData = ref({
  email: '',
  password: ''
})
const signinRes = ref('')

const signIn = async () => {
  console.log(`${api}/users/sign_in`)

  try {
    const res = await axios.post(`${api}/users/sign_in`, signInData.value)
    console.log(res)
    signinRes.value = res.data.token
    document.cookie = `customTodoToken=${res.data.token};`
  } catch (error) {
    console.log(error)
  }
}

const user = ref({
  nickname: '',
  uid: ''
})

onMounted(async () => {
  const token = document.cookie.replace(
    /(?:(?:^|.*;\s*)customTodoToken\s*=\s*([^;]*).*$)|^.*$/,
    '$1'
  )
  console.log(token)

  const res = await axios.get(`${api}/users/checkout`, {
    headers: {
      Authorization: token
    }
  })
  console.log(res)
  user.value = res.data
})

const tokenSignOut = ref('')

const signOut = async () => {
  try {
    const res = await axios.post(
      `${api}/users/sign_out`,
      {},
      {
        headers: {
          Authorization: tokenSignOut.value
        }
      }
    )
    console.log(res.data)
  } catch (error) {
    tokenSignOut.value = '登出錯誤: ' + error.message
  }
}
</script>

<template>
  {{ signUpData }}<br />
  UID:{{ signupRes }}
  <div class="box">
    <input type="email" placeholder="請輸入email" v-model="signUpData.email" />
    <input type="password" placeholder="請輸入password" v-model="signUpData.password" />
    <input type="text" placeholder="請輸入nickname" v-model="signUpData.nickname" />
    <button type="button" @click="signup">註冊</button>
  </div>
  <hr />
  {{ signInData }}<br />
  {{ signinRes }}
  <div class="box">
    <input type="email" placeholder="請輸入email" v-model="signInData.email" />
    <input type="password" placeholder="請輸入password" v-model="signInData.password" />
    <button type="button" @click="signIn">登入</button>
  </div>

  <h2>驗證</h2>
  <div v-if="user.uid">
    <p>UID:{{ user.uid }}</p>
    <p>nickname:{{ user.nickname }}</p>
  </div>
  <div v-else>您還未登入</div>

  <div class="box">
    <button type="button" @click="signOut">登出</button>
  </div>
</template>

<style>
.box {
  width: 1000px;
  text-align: center;
  margin: 50px auto 10px auto;
}
</style>
