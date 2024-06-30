 <!--
 * @FileDescription: oauth认证登录demo。
 * @Author: flying-forever
 -->
 
<script>
import axios from 'axios'


// ------------------------------ 0 路由管理 ------------------------------


const route_head = 'http://localhost:3002/api'
// const route_head = 'http://43.139.70.152:3002/api'
const routes = {  // tabs -> routes
  token: '/oauth/token',
  user: '/user',

  task_doing: '/task_doing',
  task_done: '/task_done',
  task_create: '/task_create',
  task_delete: '/task_delete',
  task_commit: '/task_submit'
}
for (const key in routes) {
  routes[key] = route_head + routes[key]
}


// ------------------------------ 1 导出组件 ------------------------------


export default {
  data() {
    return {
      name: 'null',
      level: -1,
    }
  },
  methods: {
    /** 使用令牌获取用户信息 */
    async setuser() {
      var token_type = window.sessionStorage.getItem('token_type')
      var access_token = window.sessionStorage.getItem('access_token')
      const res = await axios.get(routes.user, { headers: { 'Authorization': token_type + ' ' + access_token } })
      
      console.log(res.data)
  
      this.name = res.data.name
      this.level = res.data.level
    },
    /** 从表单获取数据登录，保存token到本地window.sessionStorage */
    async login(username, password) {
      var url = routes.token

      console.log(username, password)
      console.log(url)
      const params = new URLSearchParams({
        username: username,
        password: password,
        grant_type: 'password'
      });
      const res = await axios.post(url, params, { headers: { 'Content-Type': 'application/x-www-form-urlencoded' } });

      console.log(res.data)
      window.sessionStorage.setItem('token_type', res.data.token_type);
      window.sessionStorage.setItem('access_token', res.data.access_token);

      this.setuser()  // 得引用当前组件的实例
    },
  },
}
</script>


<template>
  <h1>StrongFront</h1>
  <h2>{{ username }}</h2>
  level {{ level }}
  <form @submit.prevent="login(username, password)">
    <input type="text" v-model="username" placeholder="username">
    <input type="text" v-model="password" placeholder="password">
    <input type="submit" value="login">
  </form>
</template>


<style scoped>
.h {
  color: cadetblue;
}
</style>
