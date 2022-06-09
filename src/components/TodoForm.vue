<template lang="">
  <div v-if="loading">Loading...</div>
  <form 
  v-else
  @submit.prevent="onSave"
  >
    <div class="row">
      <div class="col-6">
        <!-- <div class="form-group">
          <label>Subject</label>
          <input type="text" class="form-control" v-model="todo.subject"/>
          <div v-if="subjectError" class="text-red">{{subjectError}}</div>
        </div> -->
        <!-- <Input 
        label="subject" 
        :error="subjectError" 
        :subject="todo.subject"
        @update-subject="updateTodoSubject"
        /> -->
        <Input 
        label="subject" 
        v-model:subject="todo.subject"
        :error="subjectError" 
        />
      </div>
      <div v-if="editing" class="col-6">
        <div class="form-group">
          <label>Ststus</label>
          <div>
            <button 
            class="btn"
            type="button" 
            :class="todo.completed ? 'btn-success' : 'btn-danger'"
            @click="toggleTodoStatus"
            >{{ todo.completed ? 'Completed' : 'Incompleted'}}</button>
          </div>
        </div>
      </div>
      <div class="col-12">
        <div class="form-group">
          <label>body</label>
          <textarea v-model="todo.body" class="form-control" cols="30" rows="10"></textarea>
        </div>
      </div>
    </div>
    <button 
    type="submit" 
    class="btn btn-primary"
    :disabled="!todoUpdated"
    >{{ editing ? 'Update' : 'Create' }}</button>
    <button class="btn btn-outline-lighten ml-2" @click="moveTodoListPage">Cancel</button>
  </form>
  <transition name="fade">
    <Toast 
    v-if="showToast"
    :message="toastMessage"
    :type="toastAlertType"
    />
  </transition>
  
</template>
<script>
import { useRoute, useRouter } from 'vue-router';
import axios from '@/axios'
import { ref } from 'vue';
import { computed, onUnmounted, onUpdated } from 'vue';
import _ from 'lodash'
import Toast from '@/components/Toast';
import Input from '@/components/Input';
import { useToast } from '@/composables/toast'

export default {
  components: {
    Toast,
    Input
  },
  props: {
    editing: {
      type:Boolean,
      default: false,
      body: ''
    }
  },
    setup(props) {
    
    const route = useRoute()
    const router = useRouter()
    const todo = ref({
      subject: '',
      completed: false,
      body: ''
    });

    onUpdated(() => {
      console.log(todo.value.subject);
    })
    const subjectError = ref('')
    const originTodo = ref(null)
    const loading = ref(false)

    // const showToast = ref(false)
    // const toastMessage = ref('')
    // const toastAlertType = ref('')
    // const timeout = ref(null)
    // const triggerToast = (message, type='success') => {
    //   toastMessage.value= message
    //   toastAlertType.value = type
    //   showToast.value = true;
    //   timeout.value = setTimeout(()=>{
    //     console.log('hello');
    //     toastMessage.value= '';
    //     toastAlertType.value= '';
    //     showToast.value= false
    //   }, 5000)
    // }

    const { 
      showToast, 
      toastMessage, 
      toastAlertType, 
      triggerToast, 
      timeout 
    } = useToast()

    const todoId = route.params.id
    onUnmounted(()=>{
      console.log('onUnmounted');
      clearTimeout(timeout.value)
    })

    /* const updateTodoSubject = (newValue)=> {
      todo.value.subject = newValue
    } */

    const getTodo = async () => {
      loading.value = true
      try {
        const res = await axios.get(`todos/${todoId}`)
        todo.value = {...res.data}
        originTodo.value = {...res.data}
        loading.value = false
        } catch(error) {
          loading.value = false
          console.log(error);
          triggerToast('Something went wrong', 'danger')
      }
    }

    const todoUpdated =  computed(() => {
      return !_.isEqual(todo.value, originTodo.value)
    })
    const toggleTodoStatus = ()=> {
      console.log('toggleTodoStatus');
      todo.value.completed = !todo.value.completed
    }
    const moveTodoListPage = () => {
      router.push({
        name: 'Todos'
      })
    }
    if(props.editing) {
      getTodo()
    }

    const onSave = async()=> {
      subjectError.value = '';
      if(!todo.value.subject){
        subjectError.value = 'Subject is required';
        return;
      }
      try {
        let res;
        const data = {
          subject: todo.value.subject,
          completed: todo.value.completed,
          body: todo.value.body,
        }
        console.log('onSave', data);
         if(props.editing) {
          res= await axios.put(`todos/${todoId}`, data);
          originTodo.value=  {...res.data}
        } else {
          res =await axios.post('todos', data);
          todo.value.subject= ''
          todo.value.body= ''
        }
        
        
        const message = 'Successfully ' + (props.editing ? 'updated!!!!!' : 'Created!');
        triggerToast(message, 'success')

        if(!props.editing) {
          router.push({
            name: 'Todos'
          })
        }

      } catch(error) {
        console.log(error);
        triggerToast('Something went wrong', 'danger')
      }
    }
    return {
      todo,
      loading,
      toggleTodoStatus,
      moveTodoListPage,
      onSave,
      todoUpdated,
      showToast,
      toastMessage,
      toastAlertType,
      subjectError,
      Input,
      // updateTodoSubject
    }
  }
}
</script>
<style scoped>
.text-red {color: red;}
.fade-enter-active,
.fade-leave-active {
  transition: all 0.5s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translateY(-30px);
}

.fade-enter-to,
.fade-leave-from {
  opacity: 1;
  transform: translateY(0px);
}
</style>