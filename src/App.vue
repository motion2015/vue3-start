<template>
  <div id="bg"></div>
  
  <nav class="navbar navbar-expand-lg navbar-light bg-light">
    <router-link class="navbar-brand" :to="{name: 'Home'}">Kossie Coder</router-link>
    <ul class="navbar-nav mr-auto">
      <li class="nav-item active">
        <router-link class="nav-link" :to="{name: 'Todos'}">Todos</router-link>
      </li>
      <li class="nav-item">
        <router-link class="nav-link" :to="{name: 'Login'}">Login</router-link>
      </li>
      <li class="nav-item">
        <router-link class="nav-link" :to="{name: 'SassTest'}">SassTest</router-link>
      </li>
    </ul>
  </nav>
<div class="container">
  <router-view /> 
  <Toast 
    v-if="showToast"
    :message="toastMessage"
    :type="toastAlertType"
    />
</div>

  
</template>
<script>
import Toast from '@/components/Toast'
import { useToast } from '@/composables/toast'
export default {
  components: {
    Toast
  },
  setup() {
     const { 
      showToast, 
      toastMessage, 
      toastAlertType, 
      triggerToast, 
    } = useToast()

    console.log("app>>>>",showToast.value);
    return {
      showToast, 
      toastMessage, 
      toastAlertType,
      triggerToast
    }
  }
}
</script>
