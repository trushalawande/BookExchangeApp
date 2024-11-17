<script lang="ts">
  import { signUpWithEmail, loginWithGoogle } from '$lib/auth';
  import { goto } from '$app/navigation';
  
  let email = '';
  let password = '';
  let confirmPassword = '';
  let error = '';
  
  async function handleSignup() {
    if (password !== confirmPassword) {
      error = 'Passwords do not match';
      return;
    }
  
    const result = await signUpWithEmail(email, password);
    if (result.success) {
      goto('/');
    } else {
      error = result.error;
    }
  }

  async function handleGoogleLogin() {
    const result = await loginWithGoogle();
    if (result.success) {
      goto('/');
    } else {
      error = result.error;
    }
  }
</script>

<div class="min-h-screen bg-gray-100 py-12 px-4 sm:px-6 lg:px-8">
  <div class="max-w-3xl mx-auto">
    <div class="bg-white shadow-xl rounded-lg overflow-hidden glass-effect">
      <h1 class="text-3xl font-bold text-gray-900 text-center mb-8 pt-8">Create Account</h1>
      <div class="px-4 py-5 sm:p-6">
        {#if error}
          <div class="p-4 mb-4 text-red-700 bg-red-100 rounded-lg">
            {error}
          </div>
        {/if}
  
        <form on:submit|preventDefault={handleSignup} class="mb-8">
          <div class="mb-4">
            <label for="email" class="block text-sm font-medium text-gray-700 mb-2">
              Email
            </label>
            <input 
              type="email" 
              id="email" 
              bind:value={email} 
              required
              class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
            >
          </div>
  
          <div class="mb-4">
            <label for="password" class="block text-sm font-medium text-gray-700 mb-2">
              Password
            </label>
            <input 
              type="password" 
              id="password" 
              bind:value={password} 
              required
              class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-black-500"
            >
          </div>
  
          <div class="mb-6">
            <label for="confirm-password" class="block text-sm font-medium text-gray-700 mb-2">
              Confirm Password
            </label>
            <input 
              type="password" 
              id="confirm-password" 
              bind:value={confirmPassword} 
              required
              class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-black-500"
            >
          </div>
  
          <button 
            type="submit" 
            class="w-full bg-black text-white py-2 px-4 rounded-md hover:bg-black mb-4"
          >
            Sign Up
          </button>
        </form>
        <div>
          <button 
            class="w-full bg-red-500 text-white py-2 px-4 rounded-md hover:bg-red-600 mb-4"
            on:click={handleGoogleLogin}
          >
            Sign up with Google
          </button>
        </div>
        <div class="text-center">
          <a href="/login" class="text-gray-600 hover:text-gray-800">
            Already have an account? Login
          </a>
        </div>
      </div>
    </div>
  </div>
</div>

<style>
  .glass-effect {
    background: rgba(255, 255, 255, 0.2);
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.3);
  }

  .min-h-screen {
    background: linear-gradient(135deg, #5f0f40, #e36414);
  }

  .text-gray-900 {
    color: #ffffff;
  }

  .text-gray-700 {
    color: #d1d5db;
  }

  .bg-gray-100 {
    background-color: rgba(255, 255, 255, 0.2);
  }

  .bg-red-100 {
    background-color: rgba(255, 0, 0, 0.2);
  }

  .bg-black {
    background-color: rgba(0, 0, 0, 0.7);
  }

  .hover\:bg-black:hover {
    background-color: rgba(0, 0, 0, 0.9);
  }

  .bg-red-500 {
    background-color: rgba(255, 0, 0, 0.5);
  }

  .hover\:bg-red-600:hover {
    background-color: rgba(255, 0, 0, 0.9);
  }
</style>
