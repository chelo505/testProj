<template>
  <v-card class="mx-auto mt-6" max-width="400">
    <v-card-title class="text-center">Login</v-card-title>
    <v-card-text>
      <v-form @submit.prevent="handleSubmit">
        <v-text-field
          v-model="email"
          label="Email"
          type="email"
          required
          :error-messages="emailError"
        ></v-text-field>

        <v-text-field
          v-model="password"
          label="Password"
          type="password"
          required
          :error-messages="passwordError"
        ></v-text-field>

        <v-alert
          v-if="error"
          type="error"
          class="mb-4"
        >
          {{ error }}
        </v-alert>

        <v-btn
          type="submit"
          color="primary"
          block
          :loading="loading"
        >
          Login
        </v-btn>
      </v-form>
    </v-card-text>
  </v-card>
</template>

<script>
import { ref, computed } from 'vue';
import { useStore } from 'vuex';
import { useRouter } from 'vue-router';

export default {
  name: 'LoginForm',
  
  setup() {
    const store = useStore();
    const router = useRouter();
    
    const email = ref('');
    const password = ref('');
    const emailError = ref('');
    const passwordError = ref('');
    
    const loading = computed(() => store.getters['auth/isLoading']);
    const error = computed(() => store.getters['auth/authError']);
    
    const validateForm = () => {
      let isValid = true;
      emailError.value = '';
      passwordError.value = '';
      
      if (!email.value) {
        emailError.value = 'Email is required';
        isValid = false;
      }
      
      if (!password.value) {
        passwordError.value = 'Password is required';
        isValid = false;
      }
      
      return isValid;
    };
    
    const handleSubmit = async () => {
      if (!validateForm()) return;
      
      try {
        await store.dispatch('auth/login', {
          email: email.value,
          password: password.value
        });
        
        router.push('/dashboard');
      } catch (error) {
        // Error is handled by the store
      }
    };
    
    return {
      email,
      password,
      emailError,
      passwordError,
      loading,
      error,
      handleSubmit
    };
  }
};
</script> 