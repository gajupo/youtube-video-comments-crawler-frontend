<template>
  <nav>
    <a href="#" v-if="isLoggedIn" @click="logout">LogOut</a>
  </nav>
  <router-view/>
</template>

<script>
import Cookies from 'js-cookie';
import { useRouter } from 'vue-router';
import { ref, onMounted, onUnmounted } from 'vue';
import emitter from './services/eventBus'

export default {
  setup() {
    const router = useRouter();
    const isLoggedIn = ref(!!Cookies.get('userToken'));

    const updateAuthStatus = (event) => {
      isLoggedIn.value = event.loggedIn;
    };

    onMounted(() => {
      emitter.on('authChange', updateAuthStatus);
    });

    onUnmounted(() => {
      emitter.off('authChange', updateAuthStatus);
    });

    const logout = () => {
      Cookies.remove('userToken');
      emitter.emit('authChange', { loggedIn: false });
      router.push({ name: 'Login' });
    };

    return { logout, isLoggedIn };
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
</style>
