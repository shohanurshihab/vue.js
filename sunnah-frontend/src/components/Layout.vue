<script setup>
import axios from 'axios'
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'

import AdminNavs from './AdminNavs.vue'
import EmployeeNavs from './EmployeeNavs.vue'
import CustomerNavs from './CustomerNavs.vue'

const route = useRoute()
const router = useRouter()

const userData = ref({
  Name: '',
  Type: ''
})

onMounted(() => {
  const userEmail = localStorage.getItem('userEmail')
  if (localStorage.getItem('authToken')) {
    axios
      .get(`api/user?email=${userEmail}`)
      .then(response => (userData.value = response.data))
      .catch(error => console.log(error.response))
  }
})

const submitLogout = () => {
  localStorage.removeItem('authToken')
  localStorage.removeItem('userName')
  router.push('/login')
}
</script>

<template>
  <main class="flex ">
    <aside class="w-2/12 bg-white h-screen">
      <RouterLink to="/">
        <img src="../assets/logo.png" alt="Logo" class="mx-auto h-32 w-32 mt-8" />
      </RouterLink>

      <!-- Navigation routes -->
      <AdminNavs v-if="userData.Type === 'Admin'" />
      <EmployeeNavs v-else-if="userData.Type === 'Employee'" />
      <CustomerNavs v-else-if="userData.Type === 'Customer'" />

      <RouterLink to="/settings" class="flex items-center mt-6 mx-6 hover:bg-slate-100 px-7 py-1 rounded-md ">
        <img src="../assets/icons/settings.png" class="h-6 w-6" />
        <span class="ml-7 text-gray-900 text-lg">Settings</span>
      </RouterLink>

      <button @click="submitLogout" class="flex items-center mt-6 mx-6 hover:bg-slate-100 px-7 py-1 rounded-md">
        <img src="../assets/icons/logout.png" class="h-6 w-6" />
        <span class="ml-7 text-gray-900 text-lg">Logout</span>
      </button>
    </aside>

    <main class="bg-gray-50 flex-1 p-3">
      <header class="ml-12 flex justify-between items-center">
        <h1 class="font-semibold text-3xl tracking-wide uppercase text-gray-900">{{ route.meta.title }}</h1>

        <div class="flex flex-col items-center justify-center">
         <RouterLink to="/settings"> <img :src="userData.Image" alt="User" class="h-28 w-28 rounded-full" /> </RouterLink>

          <p class="mt-2 font-semibold tracking-wide">{{ userData.Name }}</p>
          <span class="text-sm font-light">{{ userData.Type }}</span>
        </div>
      </header>

      <!-- Routes will show up here -->
      <RouterView />
    </main>
  </main>
</template>

<style scoped>
.router-link-active {
  @apply bg-slate-100;
}
</style>
