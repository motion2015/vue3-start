<template>
  <form 
	@submit.prevent="onSubmit"
	class="container"
>
	<Input v-model="email" />
  <div class="form-group">
    <label>password</label>
    <input v-model="password" type="password" class="form-control" />
  </div>
  <button type="submit" class="btn btn-primary">Sumbit</button>
  </form>
  <div class="boxCount">
    {{count}}
    <button @click="count++">Add</button>
  </div>
</template>
<script>
import { ref } from 'vue'
import Input from '@/components/EmailInput.vue';
import { useCount } from '@/composables/count'
export default {
  name: 'LoginpPage',
  components: {
    Input
  },
  setup() {
		const email = ref('')
		const password = ref('')
    const { count } = useCount()
    console.log(count);
    const onSubmit =()=> {
      console.log(email.value, password.value)      
    }
    return {
      email,
      password,
      onSubmit,
      count
    }
  }
}
</script>
