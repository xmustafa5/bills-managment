<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { Form } from 'vee-validate'
import * as yup from 'yup'
import { AuthStorageUtils } from '@/utils/auth/auth-storage-utils'

const isLoading = ref(false)
const schema = yup.object({
  email: yup.string().required().email(),
  password: yup.string().required().min(8),
})
const router = useRouter()

const credentials = ref({
  email: '',
  password: '',
})

const handleLogin = async (v) => {
  isLoading.value = true
  await baseService
    .create('api/login', credentials.value)
    .then((res) => {
      AuthStorageUtils.saveAccessTokenToLocalStorage(res?.token)
      router.push('/')
    })
    .finally(() => {
      isLoading.value = false
    })
}

// Function to fill in demo credentials
const fillDemoCredentials = () => {
  credentials.value.email = 'admin@gmail.com'
  credentials.value.password = 'admin123'
}
</script>

<template>
  <div
    class="min-h-screen flex items-center justify-center p-4 bg-gradient-to-br from-black to-[#131415]"
  >
    <div
      class="max-w-md w-full bg-[#131415] rounded-xl shadow-2xl overflow-hidden border border-[#2c2d2e]"
    >
      <!-- Header with gradient -->
      <div class="bg-gradient-to-r from-indigo-600 to-blue-500 p-6">
        <h2 class="text-2xl font-bold text-white mb-1 text-center">Welcome Back</h2>
        <p class="text-indigo-100 text-center text-sm">Sign in to access your account</p>
      </div>

      <!-- Demo account banner -->
      <div class="bg-[#1c1d1f] border-b border-[#2c2d2e] p-3">
        <div class="flex items-center justify-between">
          <div class="text-sm text-[#e5e5e5]">
            <span class="text-indigo-400 font-medium">Demo Account:</span>
            admin@gmail.com / admin123
          </div>
          <button
            @click="fillDemoCredentials"
            class="text-xs bg-indigo-600 hover:bg-indigo-700 text-white px-2 py-1 rounded transition-colors"
          >
            Use Demo
          </button>
        </div>
      </div>

      <!-- Form section -->
      <div class="p-8">
        <Form class="space-y-5" :validation-schema="schema" @submit="handleLogin">
          <div class="space-y-1">
            <BaseTextField
              v-model="credentials.email"
              name="email"
              label="Email Address"
              type="email"
              placeholder="your@email.com"
              required
              class="focus-within:border-indigo-500"
            />
          </div>

          <div class="space-y-1">
            <BaseTextField
              v-model="credentials.password"
              name="password"
              label="Password"
              type="password"
              placeholder="••••••••"
              required
              class="focus-within:border-indigo-500"
            />
          </div>

          <div class="flex items-center justify-between text-sm mt-2">
            <div class="flex items-center">
              <input
                id="remember-me"
                type="checkbox"
                class="h-4 w-4 rounded border-gray-600 bg-[#1c1d1f] text-indigo-600 focus:ring-indigo-500"
              />
              <label for="remember-me" class="ml-2 block text-[#e5e5e5]">Remember me</label>
            </div>
            <a href="#" class="text-indigo-400 hover:text-indigo-300 transition-colors"
              >Forgot password?</a
            >
          </div>

          <button
            type="submit"
            class="w-full bg-gradient-to-r from-indigo-600 to-blue-500 hover:from-indigo-700 hover:to-blue-600 text-white font-medium py-3 rounded-lg transition-all shadow-md hover:shadow-lg transform hover:-translate-y-0.5"
          >
            <BaseLoadingSpinner v-if="isLoading" />
            <span v-else class="flex items-center justify-center gap-2">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="h-5 w-5"
                viewBox="0 0 20 20"
                fill="currentColor"
              >
                <path
                  fill-rule="evenodd"
                  d="M5 9V7a5 5 0 0110 0v2a2 2 0 012 2v5a2 2 0 01-2 2H5a2 2 0 01-2-2v-5a2 2 0 012-2zm8-2v2H7V7a3 3 0 016 0z"
                  clip-rule="evenodd"
                />
              </svg>
              Sign In
            </span>
          </button>
        </Form>
      </div>
    </div>

    <!-- Optional: Add a subtle brand element -->
    <div class="absolute bottom-4 text-center w-full text-[#797c7f] text-xs">
      © 2024 Bills Management System. All rights reserved.
    </div>
  </div>
</template>
