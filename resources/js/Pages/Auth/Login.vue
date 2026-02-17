<script setup>
import { Head, Link, useForm } from '@inertiajs/vue3'
import TextInput from '@/Shared/TextInput.vue'
import LoadingButton from '@/Shared/LoadingButton.vue'
import Logo from '@/Shared/Logo.vue'

defineProps({
  errors: Object,
})

const form = useForm({
  email: 'johndoe@example.com',
  password: 'secret',
  remember: false,
})

const submit = () => {
  form
    .transform(data => ({
      ...data,
      remember: form.remember ? 'on' : '',
    }))
    .post('/login')
}
</script>

<template>
  <div class="flex min-h-screen items-center justify-center bg-indigo-900 p-6">
    <div class="w-full max-w-md">
      <Head title="Login" />
      
      <Logo class="mx-auto mb-8 block h-16 w-16 fill-white" />

      <!-- INFO BANNER -->
      <div class="mb-6 rounded-lg border-l-4 border-blue-500 bg-blue-50 p-4 shadow-sm">
        <div class="flex">
          <div class="flex-shrink-0">
            <svg class="h-5 w-5 text-blue-500" fill="currentColor" viewBox="0 0 20 20">
              <path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7-4a1 1 0 11-2 0 1 1 0 012 0zM9 9a1 1 0 000 2v3a1 1 0 001 1h1a1 1 0 100-2v-3a1 1 0 00-1-1H9z" clip-rule="evenodd"/>
            </svg>
          </div>
          <div class="ml-3">
            <p class="text-sm font-semibold text-blue-800">
              🚀 DevOps Demo Project
            </p>
            <p class="mt-1 text-xs text-blue-700">
              Production-ready Laravel CRM with Docker, CI/CD & AWS deployment.
            </p>
            <p class="mt-2 text-xs text-blue-600">
              Built by <strong>Denis Basic</strong> as a DevOps portfolio demonstration.
              <a 
                href="https://github.com/dencybb/DevOps-Demo" 
                target="_blank"
                class="ml-1 font-medium underline hover:text-blue-800"
              >
                View on GitHub →
              </a>
            </p>
          </div>
        </div>
      </div>

      <!-- LOGIN FORMA -->
      <div class="overflow-hidden rounded-lg bg-white shadow">
        <form @submit.prevent="submit">
          <div class="px-8 py-6">
            <h1 class="text-center text-3xl font-bold">Welcome Back!</h1>
            <div class="mt-6">
              <TextInput v-model="form.email" :error="errors.email" class="mt-6" label="Email" type="email" autofocus autocapitalize="off" />
              <TextInput v-model="form.password" :error="errors.password" class="mt-6" label="Password" type="password" />
              <label class="mt-6 flex select-none items-center" for="remember">
                <input id="remember" v-model="form.remember" class="mr-1" type="checkbox" />
                <span class="text-sm">Remember Me</span>
              </label>
            </div>
          </div>
          <div class="flex items-center justify-between border-t border-gray-100 bg-gray-50 px-8 py-4">
            <Link class="hover:underline" href="/password/reset">Forget your password?</Link>
            <LoadingButton :loading="form.processing" class="btn-indigo ml-auto" type="submit">Login</LoadingButton>
          </div>
        </form>
      </div>

      <!-- DEMO CREDENTIALS -->
      <div class="mt-4 text-center text-sm text-gray-200">
        <p class="font-medium">Demo Credentials:</p>
        <p class="mt-1">Email: <code class="rounded bg-indigo-800 px-2 py-1 text-xs text-white">johndoe@example.com</code></p>
        <p>Password: <code class="rounded bg-indigo-800 px-2 py-1 text-xs text-white">secret</code></p>
      </div>
    </div>
  </div>
</template>
