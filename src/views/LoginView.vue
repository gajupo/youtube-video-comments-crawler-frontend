<template>
    <div class="container mt-5">
        <div class="mb-3">
            <h3>YouTube Video Comments Crawler</h3>
        </div>

      <form @submit.prevent="login">
        <div class="mb-3">
          <label for="password" class="form-label">Password</label>
          <input type="password" class="form-control" id="password" v-model="password">
        </div>
        <button type="submit" class="btn btn-primary">Login</button>
      </form>
    </div>
  </template>
  
  <script>
  import { ref } from 'vue';
  import { useRouter } from 'vue-router';
  import axios from 'axios';
  import Cookies from 'js-cookie';
  import emitter from '../services/eventBus'
  
  export default {
    setup() {
      const password = ref('');
      const router = useRouter();
  
      const login = async () => {
        try {
          const response = await axios.post('http://localhost:8081/api/auth/login', { password: password.value });
          Cookies.set('userToken', response.data.token, { secure: true, sameSite: 'Strict' });
          emitter.emit('authChange', { loggedIn: true });
          router.push({ name: 'Comments' });
        } catch (error) {
          console.error(error);
          alert('Login failed!');
        }
      };
  
      return { password, login };
    }
  };
  </script>
  